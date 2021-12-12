## 跳转控制语句

### goto

#### 介绍：

1、Golang 的 goto 语句可以无条件地转移到程序中指定的行

2、goto 语句通常与条件语句配合使用。可以用来实现条件转移，跳出循环体等功能

#### 基本语法

```go
goto label
...
label: statement
```

举例：

```go
func main() {
	var n = 10

	if n > 8 {
		goto more
	}
	fmt.Println("这行代码不会执行")
more:
	fmt.Println("跳转到这里")
}
```

### return

#### 介绍：

return 使用在方法或者函数中，表示跳出所在的方法或者函数

```go
func main() {
	for i := 0; i < 10; i++ {
		if i == 3 {
			return
		}
		fmt.Println(i)
	}
	fmt.Println("end")
}

打印：
0
1
2
```

