## [排错] Linux 解决取消挂载目录时报错 “target is busy” 或者 “device is busy”

## 具体的操作步骤：

#### 步骤一：取消挂载时可能出现的报错

当取消某一个目录的挂载时，出现以下报错，则说明此目录可能正在被使用：

（1）当使用 umount 命令时出现类似 “umount: /xxx: target is busy.” 等字样
（2）当使用 umount 命令时出现类似 “umount: /xxx: device is busy.” 等字样

#### 步骤二：查看要被取消挂载的目录正在被哪个进程使用

（注意：以下两种方法二选一执行即可）

#### 2.1 方法一：使用 fuser 命令查看此目录正在被哪个进程使用 2.1.1 安装 psmisc

```
# yum install psmisc
```

#### 2.1.2 查看要取消挂载的目录正在被哪个进程使用

```
# fuser -mv /mnt/
                     USER        PID ACCESS COMMAND
/mnt:                root     kernel mount /mnt
                     root      11111 ..c.. bash
```

#### 2.2 方法二：使用 lsof 命令查看此目录正在被哪个进程使用

```
# lsof /mnt/
COMMAND   PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
bash    11111 root  cwd    DIR   3,16       51   128 /mnt
```

#### 步骤三：杀死正在使用此目录的进程

```
# kill -9 11111
```

#### 步骤四：此时就可以取消挂载了

```
# umount /mnt/
```