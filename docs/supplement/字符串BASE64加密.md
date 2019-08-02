```java
 /**
  * 字符串BASE64加密
  * @param str
  * @return
  */
public static String getBASE64(String str) {
    byte[] b = str.getBytes();
    String s = null;
    if (b != null) {
        s = new BASE64Encoder().encode(b);
    }
    return s;
}
```

