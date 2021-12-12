Go 语言保证了既能达到静态编译语言的安全和性能，又达到了动态语言开发维护的高效率。

1、从 C 语言中继承了很多理念，包括表达式语法，控制结构，基础数据结构，调用参数传值，指针等等，也保留了和 C 语言一样的编译执行方式及弱化的指针。

```go
func testPtr(num *int) {
	*num = 20
}
```

2、引入包的概念，用于组织程序结构，Go 语言的 `一个文件都要归属于一个包`，而不能单独存在

3、垃圾回收机制，内存自动回收，不需开发人员管理

4、天然并发

	- 从语言层面支持并发，实现简单
	- goroutine，轻量级线程，可实现大并发处理，高效利用多核
	- 基于 CPS 并发模型（Communicating Sequential Processes）实现

5、吸收了管道通信机制，形成 Go 语言特有的管道 channel，通过管道 channel，可以实现不同的 goroute 之间的相互通信。

6、函数返回多个值

```go
func getSumAndSub(n1 int, n2 int) (int, int) {
	sum := n1 + n2
	sub := n1 - n2
	return sum, sub
}

func main() {
	sum, sub := getSumAndSub(10, 5)
	fmt.Println(sum, sub)

	sum1, _ := getSumAndSub(10, 5)
	fmt.Println(sum1)
}
```

7、新的创新：比如切片、延时执行 defer 等