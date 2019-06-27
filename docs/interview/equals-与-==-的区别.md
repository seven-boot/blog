- `==` 与`equals` 的主要区别是：`==` 常用于比较原生类型，而 `equals()` 方法用于检查对象的相等性。
- 另一个不同的点是：如果 `==` 和 `equals()` 用于比较对象，当两个引用地址相同，`==` 返回 true。而 `equals()` 可以返回 true 或者 false 主要取决于重写实现。最常见的一个例子，字符串的比较，不同情况 `==` 和 `equals()` 返回不同的结果。

- 如果要用equal方法，请用object<不可能为空>.equal(object<可能为空>)) 例如： 使用 `"bar".equals(foo) `而不是`foo.equals("bar")`

