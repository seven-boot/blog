## String 类

String 类是不可改变的，所以一旦创建了 String 对象，那它的值就无法改变了。

如果需要对字符串做很多修改，那么应该选择 StringBuffer & StringBuilder 类

## StringBuffer 和 StringBuilder 类

当对字符串进行修改的时候，需要使用 StringBuffer 和 StringBuilder 类

和 String 类不同的是，StringBuffer 和 StringBuilder 类的对象能够被多次的修改，并且不产生新的未使用对象。

StringBuilder 和 StringBuffer 最大的不同在于：StringBuilder 的方法不是线程安全的（不能同步访问）。

由于 StringBuilder 相较于 StringBuffer 有速度优势，所以多数情况下建议使用 StringBuilder 类。

然而在应用程序要求线程安全的情况下，则必须使用 StringBuffer 类。

