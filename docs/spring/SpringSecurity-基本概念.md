principal：能唯一标识用户身份的属性，一个主题（用户）可以有多个principal；
举个例子：你去登录一些网站时可以用用户名，也可以用手机或邮箱，这些principal是别人可以知道的；


credential：凭证，主题（用户）才知道的；
举个例子：你给手机开锁，可以使用屏幕密码也可以使用人脸识别，屏幕密码和人脸是你个人（用户）才拥有的；

最常见的 principals 和 credentials 组合就是用户名 / 密码了。


https://docs.oracle.com/javase/8/docs/technotes/guides/security/jgss/tutorials/glossary.html

Spring Security 使用一个 Authentication 对象来描述当前用户的相关信息。SecurityContextHolder 中持有的是当前用户的 SecurityContext，而 SecurityContext 持有的是代表当前用户相关信息的 Authentication 的引用。这个 Authentication 对象不需要我们自己去创建，在与系统交互的过程中，Spring Security 会自动为我们创建相应的 Authentication 对象，然后赋值给当前的 SecurityContext。

但是往往我们需要在程序中获取当前用户的相关信息，比如最常见的是获取当前登录用户的用户名。在程序的任何地方，通过如下方式我们可以获取到当前用户的用户名。

```java
public String getCurrentUsername() {

    Object principal = SecurityContextHolder.getContext().getAuthentication().getPrincipal();

    if (principal instanceof UserDetails) {

        return ((UserDetails) principal).getUsername();

    }

    if (principal instanceof Principal) {

        return ((Principal) principal).getName();

    }

    return String.valueOf(principal);

}
```

通过 Authentication.getPrincipal() 可以获取到代表当前用户的信息，这个对象通常是 UserDetails 的实例。获取当前用户的用户名是一种比较常见的需求，关于上述代码其实Spring Security在Authentication中的实现类中已经为我们做了相关实现，所以获取当前用户的用户名最简单的方式应当如下。

```java
public String getCurrentUsername() {
    return  SecurityContextHolder.getContext().getAuthentication().getName();
}
```

此外，调用SecurityContextHolder.getContext()获取SecurityContext时，如果对应的SecurityContext不存在，则Spring Security将为我们建立一个空的SecurityContext并进行返回。