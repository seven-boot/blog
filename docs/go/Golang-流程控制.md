主要有三大流程控制语句：

1、顺序控制

2、分支控制

3、循环控制

## 顺序控制

程序自上到下逐行的执行，中间没有任何判断和跳转

> 注意：golang 中定义变量时采用合法的前向引用，即先定义后使用

## 分支控制

### if-else

让程序有选择的执行，分支控制有三种：单分支、双分支、多分支

- 单分支

```go
func main() {
	var age int
	fmt.Println("请输入年龄：")
	fmt.Scanln(&age)

	if age > 18 {
		fmt.Println("成年了")
	}
}
```

- 双分支

```go
func main() {
	var age int
	fmt.Println("请输入年龄：")
	fmt.Scanln(&age)

	if age > 18 {
		fmt.Println("成年了")
	} else {
		fmt.Println("未成年")
	}
}
```

- 多分支

```go
if age > 45 {
	fmt.Println("45")
} else if age > 30 {
	fmt.Println("30")
} else {
	fmt.Println("30以下")
}
```

- 嵌套分支

```

```

