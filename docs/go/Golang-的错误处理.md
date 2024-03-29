对于Go语言（golang）的错误设计，相信很多人已经体验过了，它是通过 `返回值` 的方式，来强迫调用者对错误进行处理，要么你忽略，要么你处理（处理也可以是继续返回给调用者），对于golang 这种设计方式，我们会在代码中写大量的`if`判断，以便做出决定。

```go
func main() {
    conent,err := ioutil.ReadFile("filepath")
    if err !=nil{
        //错误处理
    } else {
        fmt.Println(string(conent))
    }
}
```

这类代码，在我们编码中是非常常见的，大部分情况下`error`都是`nil`，也就是没有任何错误，但是非`nil`的时候，意味着错误就出现了，我们需要对他进行处理。

## error 接口

`error`其实一个接口，内置的，我们看下它的定义

```go
// The error built-in interface type is the conventional interface for
// representing an error condition, with the nil value representing no error.
type error interface {
    Error() string
}
```

它只有一个方法 `Error`，只要实现了这个方法，就是实现了`error`。现在我们自己定义一个错误试试。

```go
type fileError struct {
}

func (fe *fileError) Error() string {
    return "文件错误"
}
```

## 自定义 error

自定义了一个`fileError`类型，实现了`error`接口。现在测试下看看效果。

```go
func main() {
    conent, err := openFile()
    if err != nil {
        fmt.Println(err)
    } else {
        fmt.Println(string(conent))
    }
}

//只是模拟一个错误
func openFile() ([]byte, error) {
    return nil, &fileError{}
}
```

我们运行模拟的代码，可以看到`文件错误`的通知。

在实际的使用过程中，我们可能遇到很多错误，他们的区别是错误信息不一样，一种做法是每种错误都类似上面一样定义一个错误类型，但是这样太麻烦了。我们发现`Error`返回的其实是个 `字符串`，我们可以修改下，让这个字符串可以设置就可以了。

```go
type fileError struct {
    s string
}

func (fe *fileError) Error() string {
    return fe.s
}
```

恩，这样改造后，我们就可以在声明`fileError`的时候，设置好要提示的错误文字，就可以满足我们不同的需要了。

```go
//只是模拟一个错误
func openFile() ([]byte, error) {
    return nil, &fileError{"文件错误，自定义"}
}
```

恩，可以了，已经达到了我们的目的。现在我们可以把它变的更通用一些，比如修改`fileError` 的名字，再创建一个辅助函数，便于我们创建不同的错误类型。

```go
func New(text string) error {
    return &errorString{text}
}

type errorString struct {
    s string
}

func (e *errorString) Error() string {
    return e.s
}
```

变成以上这样，我们就可以通过`New`函数，辅助我们创建不同的错误了，这其实就是我们经常用到的`errors.New`函数，被我们一步步剖析演化而来，现在大家对Go语言(golang)内置的错误`error`有了一个清晰的认知了。

## 存在的问题

虽然Go语言对错误的设计非常简洁，但是对于我们开发者来说，很明显是不足的，比如我们需要知道出错的更多信息，在什么文件的，哪一行代码？只有这样我们才更容易的定位问题。

还有比如，我们想对返回的`error`附加更多的信息后再返回，比如以上的例子，我们怎么做呢？我们只能先通过`Error`方法，取出原来的错误信息，然后自己再拼接，再.使用`errors.New`函数生成新错误返回。

如果我们以前做过java开发，我们知道Java的异常是可以嵌套的，也就是说，通过这个，我们很容易知道错误的根本原因，因为Java的异常，是一层层的嵌套返回的，不管中间经历了多少包装，我们可以通过`cause`找到根本错误的原因。

## 解决问题

如果要解决以上的问题，那么首先我们必须再继续扩充我们的`errorString`，再增加一些字段来存储更多的信息。比如我们要记录堆栈信息。

```go
type stack []uintptr
type errorString struct {
    s string
    *stack
}
```

有了存储堆栈信息的`stack`字段，我们在生成错误的时候，就可以把调用的堆栈信息存储在这个字段里。

```go
func callers() *stack {
    const depth = 32
    var pcs [depth]uintptr
    n := runtime.Callers(3, pcs[:])
    var st stack = pcs[0:n]
    return &st
}

func New(text string) error {
    return &errorString{
        s:   text,
        stack: callers(),
    }
}
```

完美解决，现在如果再解决，对现有的错误附加一些信息的问题呢？相信大家应该有思路了。

```go
type withMessage struct {
    cause error
    msg   string
}

func WithMessage(err error, message string) error {
    if err == nil {
        return nil
    }
    return &withMessage{
        cause: err,
        msg:   message,
    }
}
```

使用`WithMessage`函数，对原来的`error`包装下，就可以生成一个新的带有包装信息的错误了。

## 推荐的方案

以上我们在解决问题是，采取的方法是不是比较熟悉？尤其是看源代码，没错，这就是`github.com/pkg/errors`这个错误处理库的源代码。

因为Go语言提供的错误太简单了，以至于简单的我们无法更好的处理问题，甚至不能为我们处理错误，提供更有用的信息，所以诞生了很多对错误处理的库，`github.com/pkg/errors`是比较简洁的一样，并且功能非常强大，受到了大量开发者的欢迎，使用者很多。

它的使用非常简单，如果我们要新生成一个错误，可以使用`New`函数,生成的错误，自带调用堆栈信息。

```go
func New(message string) error
```

如果有一个现成的`error`，我们需要对他进行再次包装处理，这时候有三个函数可以选择。

```go
//只附加新的信息
func WithMessage(err error, message string) error

//只附加调用堆栈信息
func WithStack(err error) error

//同时附加堆栈和信息
func Wrap(err error, message string) error
```

其实上面的包装，很类似于Java的异常包装，被包装的`error`，其实就是`Cause`,在前面的章节提到错误的根本原因，就是这个`Cause`。所以这个错误处理库为我们提供了`Cause`函数让我们可以获得最根本的错误原因。

```go
func Cause(err error) error {
    type causer interface {
        Cause() error
    }

    for err != nil {
        cause, ok := err.(causer)
        if !ok {
            break
        }
        err = cause.Cause()
    }
    return err
}
```

使用`for`循环一直找到最根本（最底层）的那个`error`。

以上的错误我们都包装好了，也收集好了，那么怎么把他们里面存储的堆栈、错误原因等这些信息打印出来呢？其实，这个错误处理库的错误类型，都实现了`Formatter`接口，我们可以通过`fmt.Printf`函数输出对应的错误信息。

```text
%s,%v //功能一样，输出错误信息，不包含堆栈
%q //输出的错误信息带引号，不包含堆栈
%+v //输出错误信息和堆栈
```

以上如果有循环包装错误类型的话，会递归的把这些错误都会输出。

## 小结

通过使用这个 `github.com/pkg/errors` 错误库，我们可以收集更多的信息，可以让我们更容易的定位问题。

我们收集的这些信息不止可以输出到控制台，也可以当做日志，使用输出到相应的`Log`日志里，便于分析问题。

据说这个库，会被加入到 Golang 标准 SDK 里，期待着，如果加入的话，应该就是补充现在标准库里的`errors` 这个package了。