## Boolean 类型，生成的 get 方法是 get 开头的

```java
 private Boolean success;

    public Boolean getSuccess() {
        return success;
    }

    public void setSuccess(Boolean success) {
        this.success = success;
    }
```

## boolean 类型，生成的 get 方法是 is 开头的！！！

```java
 private boolean success;

    public boolean isSuccess() {
        return success;
    }

    public void setSuccess(boolean success) {
        this.success = success;
    }
```

## 总结

* 小写的 boolean 基本类型作为类的属性时，如果这个对象涉及到反射，反射一般会默认调取对象的 get 方法，就会报错

* 用到布尔类型的属性时，最好统一使用大写的包装类 Boolean

* 如果用小写的 boolean 基本类型，最好重写 get 方法