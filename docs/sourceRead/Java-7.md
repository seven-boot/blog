> Java7开发代号是Dolphin(海豚),于2011-07-28发行.

### 特性列表

- switch中添加对String类型的支持
- 数字字面量的改进 / 数值可加下划
- 异常处理（捕获多个异常） try-with-resources
- 增强泛型推断
- JSR203 NIO2.0（AIO）新IO的支持
- JSR292与InvokeDynamic指令
- Path接口、DirectoryStream、Files、WatchService（重要接口更新）
- fork/join framework

## 1、switch 中添加对 String 类型的支持

```java
public String generate(String name, String gender) {  
    String title = "";  
    switch (gender) {  
        case "男":  
            title = name + " 先生";  
            break;  
        case "女":  
            title = name + " 女士";  
            break;  
        default:  
            title = name;  
    }  
    return title;  
}
```

编译器在编译时先做处理：

1. case仅仅有一种情况。直接转成if。
2. 假设仅仅有一个case和default，则直接转换为if…else…。
3. 有多个case。先将String转换为hashCode，然后相应的进行处理，JavaCode在底层兼容Java7曾经版本号。

## 2、数字字面量的改进

Java7前支持十进制（123）、八进制（0123）、十六进制（0X12AB）
Java7添加二进制表示（0B11110001、0b11110001）
数字中可加入分隔符
Java7中支持在数字量中间添加’_'作为分隔符。更直观，如（12_123_456）。下划线仅仅能在数字中间。编译时编译器自己主动删除数字中的下划线。

```java
int one_million = 1_000_000;
```

## 3、异常处理（捕获多个异常）try-with-resources

catch子句能够同一时候捕获多个异常

```java
public void testSequence() {  
    try {  
        Integer.parseInt("Hello");  
    }  
    catch (NumberFormatException | RuntimeException e) {  //使用'|'切割，多个类型，一个对象e  

    }  
}  
```

try-with-resources语句
Java7之前须要在finally中关闭socket、文件、数据库连接等资源；
Java7中在try语句中申请资源，实现资源的自己主动释放（资源类必须实现java.lang.AutoCloseable接口，一般的文件、数据库连接等均已实现该接口，close方法将被自己主动调用）。

```java
public void read(String filename) throws IOException {  
    try (BufferedReader reader = new BufferedReader(new FileReader(filename))) {  
        StringBuilder builder = new StringBuilder();  
        String line = null;  
        while((line=reader.readLine())!=null){  
            builder.append(line);  
            builder.append(String.format("%n"));  
        }  
        return builder.toString();  
    }   
} 
```

## 4、增强类型推断

pre-java 7

```java
Map<String, List<String>> map = new HashMap<String, List<String>>();   
```

after-java 7

```java
Map<String, List<String>> anagrams = new HashMap<>();
```

## 5、NIO2.0（AIO）新IO的支持

- bytebuffer

```java
public class ByteBufferUsage {
    public void useByteBuffer() {
        ByteBuffer buffer = ByteBuffer.allocate(32);
        buffer.put((byte)1);
        buffer.put(new byte[3]);
        buffer.putChar('A');
        buffer.putFloat(0.0f);
        buffer.putLong(10, 100L);
        System.out.println(buffer.getChar(4));
        System.out.println(buffer.remaining());
    }
    public void byteOrder() {
        ByteBuffer buffer = ByteBuffer.allocate(4);
        buffer.putInt(1);
        buffer.order(ByteOrder.LITTLE_ENDIAN);
        buffer.getInt(0); //值为16777216
    }
    public void compact() {
        ByteBuffer buffer = ByteBuffer.allocate(32);
        buffer.put(new byte[16]);
        buffer.flip();
        buffer.getInt();
        buffer.compact();
        int pos = buffer.position();
    }
    public void viewBuffer() {
        ByteBuffer buffer = ByteBuffer.allocate(32);
        buffer.putInt(1);
        IntBuffer intBuffer = buffer.asIntBuffer();
        intBuffer.put(2);
        int value = buffer.getInt(); //值为2
    }
    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        ByteBufferUsage bbu = new ByteBufferUsage();
        bbu.useByteBuffer();
        bbu.byteOrder();
        bbu.compact();
        bbu.viewBuffer();
    }
}
```

- filechannel

```java
public class FileChannelUsage {
    public void openAndWrite() throws IOException {
        FileChannel channel = FileChannel.open(Paths.get("my.txt"), StandardOpenOption.CREATE, StandardOpenOption.WRITE);
        ByteBuffer buffer = ByteBuffer.allocate(64);
        buffer.putChar('A').flip();
        channel.write(buffer);
    }
    public void readWriteAbsolute() throws IOException {
        FileChannel channel = FileChannel.open(Paths.get("absolute.txt"), StandardOpenOption.READ, StandardOpenOption.CREATE, StandardOpenOption.WRITE);
        ByteBuffer writeBuffer = ByteBuffer.allocate(4).putChar('A').putChar('B');
        writeBuffer.flip();
        channel.write(writeBuffer, 1024);
        ByteBuffer readBuffer = ByteBuffer.allocate(2);
        channel.read(readBuffer, 1026);
        readBuffer.flip();
        char result = readBuffer.getChar(); //值为'B'
    }
    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) throws IOException {
        FileChannelUsage fcu = new FileChannelUsage();
        fcu.openAndWrite();
        fcu.readWriteAbsolute();
    }
}
```

## 6、JSR292 与 InvokeDynamic

JSR 292: Supporting Dynamically Typed Languages on the JavaTM Platform，支持在JVM上运行动态类型语言。在字节码层面支持了InvokeDynamic。

- 方法句柄MethodHandle

```java
public class ThreadPoolManager {
    private final ScheduledExecutorService stpe = Executors
            .newScheduledThreadPool(2);
    private final BlockingQueue<WorkUnit<String>> lbq;
    public ThreadPoolManager(BlockingQueue<WorkUnit<String>> lbq_) {
        lbq = lbq_;
    }
    public ScheduledFuture<?> run(QueueReaderTask msgReader) {
        msgReader.setQueue(lbq);
        return stpe.scheduleAtFixedRate(msgReader, 10, 10, TimeUnit.MILLISECONDS);
    }
    private void cancel(final ScheduledFuture<?> hndl) {
        stpe.schedule(new Runnable() {
            public void run() {
                hndl.cancel(true);
            }
        }, 10, TimeUnit.MILLISECONDS);
    }
    /**
     * 使用传统的反射api
     */
    public Method makeReflective() {
        Method meth = null;
        try {
            Class<?>[] argTypes = new Class[]{ScheduledFuture.class};
            meth = ThreadPoolManager.class.getDeclaredMethod("cancel", argTypes);
            meth.setAccessible(true);
        } catch (IllegalArgumentException | NoSuchMethodException
                | SecurityException e) {
            e.printStackTrace();
        }
        return meth;
    }
    /**
     * 使用代理类
     * @return
     */
    public CancelProxy makeProxy() {
        return new CancelProxy();
    }
    /**
     * 使用Java7的新api，MethodHandle
     * invoke virtual 动态绑定后调用 obj.xxx
     * invoke special 静态绑定后调用 super.xxx
     * @return
     */
    public MethodHandle makeMh() {
        MethodHandle mh;
        MethodType desc = MethodType.methodType(void.class, ScheduledFuture.class);
        try {
            mh = MethodHandles.lookup().findVirtual(ThreadPoolManager.class,
                    "cancel", desc);
        } catch (NoSuchMethodException | IllegalAccessException e) {
            throw (AssertionError) new AssertionError().initCause(e);
        }
        return mh;
    }
    public static class CancelProxy {
        private CancelProxy() {
        }
        public void invoke(ThreadPoolManager mae_, ScheduledFuture<?> hndl_) {
            mae_.cancel(hndl_);
        }
    }
}
```

- 调用invoke

```java
public class ThreadPoolMain {
    /**
     * 如果被继承，还能在静态上下文寻找正确的class
     */
    private static final Logger logger = LoggerFactory.getLogger(MethodHandles.lookup().lookupClass());
    private ThreadPoolManager manager;
    public static void main(String[] args) {
        ThreadPoolMain main = new ThreadPoolMain();
        main.run();
    }
    private void cancelUsingReflection(ScheduledFuture<?> hndl) {
        Method meth = manager.makeReflective();
        try {
            System.out.println("With Reflection");
            meth.invoke(hndl);
        } catch (IllegalAccessException | IllegalArgumentException
                | InvocationTargetException e) {
            e.printStackTrace();
        }
    }
    private void cancelUsingProxy(ScheduledFuture<?> hndl) {
        CancelProxy proxy = manager.makeProxy();
        System.out.println("With Proxy");
        proxy.invoke(manager, hndl);
    }
    private void cancelUsingMH(ScheduledFuture<?> hndl) {
        MethodHandle mh = manager.makeMh();
        try {
            System.out.println("With Method Handle");
            mh.invokeExact(manager, hndl);
        } catch (Throwable e) {
            e.printStackTrace();
        }
    }
    private void run() {
        BlockingQueue<WorkUnit<String>> lbq = new LinkedBlockingQueue<>();
        manager = new ThreadPoolManager(lbq);
        final QueueReaderTask msgReader = new QueueReaderTask(100) {
            @Override
            public void doAction(String msg_) {
                if (msg_ != null)
                    System.out.println("Msg recvd: " + msg_);
            }
        };
        ScheduledFuture<?> hndl = manager.run(msgReader);
        cancelUsingMH(hndl);
        // cancelUsingProxy(hndl);
        // cancelUsingReflection(hndl);
    }
}
```

## 7、Path接口(重要接口更新)

- Path

```java
public class PathUsage {
    public void usePath() {
        Path path1 = Paths.get("folder1", "sub1");
        Path path2 = Paths.get("folder2", "sub2");
        path1.resolve(path2); //folder1\sub1\folder2\sub2
        path1.resolveSibling(path2); //folder1\folder2\sub2
        path1.relativize(path2); //..\..\folder2\sub2
        path1.subpath(0, 1); //folder1
        path1.startsWith(path2); //false
        path1.endsWith(path2); //false
        Paths.get("folder1/./../folder2/my.text").normalize(); //folder2\my.text
    }
    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        PathUsage usage = new PathUsage();
        usage.usePath();
    }
}
```

- DirectoryStream

```java
public class ListFile {
    public void listFiles() throws IOException {
        Path path = Paths.get("");
        try (DirectoryStream<Path> stream = Files.newDirectoryStream(path, "*.*")) {
            for (Path entry: stream) {
                //使用entry
                System.out.println(entry);
            }
        }
    }
    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) throws IOException {
        ListFile listFile = new ListFile();
        listFile.listFiles();
    }
}
```

- Files

```java
public class FilesUtils {
    public void manipulateFiles() throws IOException {
        Path newFile = Files.createFile(Paths.get("new.txt").toAbsolutePath());
        List<String> content = new ArrayList<String>();
        content.add("Hello");
        content.add("World");
        Files.write(newFile, content, Charset.forName("UTF-8"));
        Files.size(newFile);
        byte[] bytes = Files.readAllBytes(newFile);
        ByteArrayOutputStream output = new ByteArrayOutputStream();
        Files.copy(newFile, output);
        Files.delete(newFile);
    }
    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) throws IOException {
        FilesUtils fu = new FilesUtils();
        fu.manipulateFiles();
    }
}
```

- WatchService

```java
public class WatchAndCalculate {
    public void calculate() throws IOException, InterruptedException {
        WatchService service = FileSystems.getDefault().newWatchService();
        Path path = Paths.get("").toAbsolutePath();
        path.register(service, StandardWatchEventKinds.ENTRY_CREATE);
        while (true) {
            WatchKey key = service.take();
            for (WatchEvent<?> event : key.pollEvents()) {
                Path createdPath = (Path) event.context();
                createdPath = path.resolve(createdPath);
                long size = Files.size(createdPath);
                System.out.println(createdPath + " ==> " + size);
            }
            key.reset();
        }
    }
    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) throws Throwable {
        WatchAndCalculate wc = new WatchAndCalculate();
        wc.calculate();
    }
}
```

## fork/join计算框架

Java7提供的一个用于并行执行任务的框架，是一个把大任务分割成若干个小任务，最终汇总每个小任务结果后得到大任务结果的框架。

该框架为Java8的并行流打下了坚实的基础