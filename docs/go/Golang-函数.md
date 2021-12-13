## 基本语法

```go
func 函数名 (形参列表) (返回值类型列表) {
    执行语句...
    return 返回值列表
}
```

## init 函数

每一个源文件都可以包含一个 init 函数，该函数会在 main 函数执行前，被 Go 运行框架调用，也就是说 init 会在 main 函数前被调用。

```go
func init() {
	fmt.Println("init...")
}

func main() {
	fmt.Println("main....")
}
```

输出：

```
init...
main....
```

- 如果一个文件同时包含全局变量定义、init 函数和 main 函数，则执行的流程是：全局变量定义-》init 函数-》main 函数

```go
var age = test()

func test() int {
	fmt.Println("test...")
	return 50
}

func init() {
	fmt.Println("init...")
}

func main() {
	fmt.Println("main....")
}
```

输出：

```
test...
init...
main....
```

- init 函数最主要的作用，就是完成一些初始化的工作，比如初始化 mysql 的连接

```go
var DBHelper *gorm.DB
var err error

func init() {
	dsn := "root:123456@tcp(192.168.1.12:3306)/test?charset=utf8mb4&parseTime=True&loc=Local"
	DBHelper, err = gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		log.Fatal("数据库初始化错误：", err)
		return
	}

	db, _ := DBHelper.DB()
	db.SetMaxIdleConns(10)

	db.SetMaxOpenConns(100)

	db.SetConnMaxLifetime(time.Hour)
	DBHelper.Debug()
}
```

> 注意：如果当前文件和引入的文件中都有 init 函数，执行流程为：import 包中的变量定义 -》 import 包中的 init 函数 -》 当前文件中的变量定义 -》 当前文件中的 init 函数 -》 当前文件中的 main 函数

## 匿名函数

Golang 支持匿名函数，如果某个函数只是希望使用一次，可以考虑使用匿名函数，匿名函数也可以实现多次调用

- 在定义匿名函数时就直接调用，这种方式匿名函数只能调用一次

```go
func main() {
	res := func (n1 int, n2 int) int {
		return n1 + n2
	}(45, 54)

	fmt.Println("res=", res)
}
```

- 将匿名函数赋给一个变量，再通过该变量来调用匿名函数（该变量的数据类型就是函数类型）

```go
func main() {
	doSub := func(n1 int, n2 int) int {
		return n1 -n2
	}

	res2 := doSub(10, 4)
	fmt.Println("res2=", res2)

	res3 := doSub(40, 32)
	fmt.Println("res3=", res3)
}
```

> 全局匿名函数：如果将匿名函数赋给一个全局变量，那么这个匿名函数，就成为一个全局匿名函数

```go
var (
	DoMul = func(n1 int, n2 int) int {
		return n1 * n2
	}
)

func main() {
	res4 := DoMul(3, 8)
	fmt.Println("res4=", res4)
}
```

