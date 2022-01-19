## 概述

Spring Security 默认是在配置类中使用URL进行拦截，禁止使用注解，想要开启注解支持需要在配置类上加上如下注解 `@EnableGlobalMethodSecurity`

```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE})
@Documented
@Import({GlobalMethodSecuritySelector.class})
@EnableGlobalAuthentication
@Configuration
public @interface EnableGlobalMethodSecurity {
    boolean prePostEnabled() default false;

    boolean securedEnabled() default false;

    boolean jsr250Enabled() default false;

    boolean proxyTargetClass() default false;

    AdviceMode mode() default AdviceMode.PROXY;

    int order() default 2147483647;
}
```

## 三种注解支持

需要开启哪种注解支持，就把注解中对应的属性设置为 true 即可

### @Secured

```java
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface Secured {
    String[] value();
}
```

#### 使用方式

该注解可以使用多个角色控制，并且只能使用在方法上

```
@Secured({"ROLE_USER","ROLE_ADMIN"})
```

### JSR-250 注解

- @RolesAllowed：允许的角色
- @PermitAll：允许任何人访问
- @DenyAll：拒绝任何人访问

#### 使用方式

```java
// 表示只有 USER、ADMIN 角色权限才可以访问
@RolesAllowed({"USER", "ADMIN"})
public String testAllow() {
	...
}

@PermitAll

@DenyAll
```

### prePostEnable 规范

 prePostEnabled 没有 原生的spring注解功能强大，所以其使用需要借助spring的EL表达式

- @PreAuthorize：方法执行前验证授权
- @PostAuthorize：方法执行之后验证
- @PreFilter：方法执行之前执行，用于过滤集合
- @PostFilter：方法执行之后执行，用于过滤集合

#### 使用方式

```java
@PreAuthorize("hasRole('ADMIN')")

@PreAuthorize("isAnonymous()")

@PreAuthorize("hasAuthority('ROLE_ADMIN')")
```

使用 EL 表达式判定

```java
@GetMapping("api/has")
@PreAuthorize("#sysUser.username == 'admin' ")
public String hasPerm(SysUser sysUser) {
	return "EL测试";
}
```

还可以使用 `@P` 注解获取参数

```java
@GetMapping("api/sys")
@PreAuthorize("#c.username == authentication.principal ")
public String hasPerm2(@P("c")SysUser sysUser) {
    return "EL测试";
}
```

如果是Spring Data的`@Param`注解

```java
@PreAuthorize("#n == authentication.principal")
public String hasPerm2(@Param("n")SysUser sysUser) {
    return "EL测试";
}
```

## 权限被拒绝处理器

如果当用户访问自身不具有的权限接口时，springSecurity 会将异常信息传递给 AccessDeniedHandler ；我们只需要实现它即可；示例如下

```java
/**
 * <p> 权限不足处理 </p>
 */
@Component
public class DenyHandler implements AccessDeniedHandler {

    @Override
    public void handle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, AccessDeniedException e) throws IOException, ServletException {
        // 设置响应头
        httpServletResponse.setContentType("application/json;charset=utf-8");
        // 返回值
        ResultPage result = ResultPage.error(CodeMsg.PERM_ERROR);
        httpServletResponse.getWriter().write(JSON.toJSONString(result));
    }
}
```

## 常见内置表达式

| 表达                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `hasRole([role])`                                            | 如果当前主体具有指定角色，则返回`true`。默认情况下，如果提供的角色不以“ROLE_”开头，则会添加该角色。这可以通过修改`DefaultWebSecurityExpressionHandler`上的`defaultRolePrefix`来自定义。 |
| `hasAnyRole([role1,role2])`                                  | 如果当前主体具有任何提供的角色（以逗号分隔的字符串列表给出），则返回`true`。默认情况下，如果提供的角色不以“ROLE_”开头，则会添加该角色。这可以通过修改`DefaultWebSecurityExpressionHandler`上的`defaultRolePrefix`来自定义。 |
| `hasAuthority([authority])`                                  | 如果当前主体具有指定的权限，则返回`true`。                   |
| `hasAnyAuthority([authority1,authority2])`                   | 如果当前主体具有任何提供的权限（以逗号分隔的字符串列表给出），则返回`true` |
| `principal`                                                  | 允许直接访问代表当前用户的主体对象                           |
| `authentication`                                             | 允许直接访问从`SecurityContext`获取的当前`Authentication`对象 |
| `permitAll`                                                  | 始终评估为`true`                                             |
| `denyAll`                                                    | 始终评估为`false`                                            |
| `isAnonymous()`                                              | 如果当前主体是匿名用户，则返回`true`                         |
| `isRememberMe()`                                             | 如果当前主体是remember-me用户，则返回`true`                  |
| `isAuthenticated()`                                          | 如果用户不是匿名用户，则返回`true`                           |
| `isFullyAuthenticated()`                                     | 如果用户不是匿名用户或记住我用户，则返回`true`               |
| `hasPermission(Object target, Object permission)`            | 如果用户有权访问给定权限的提供目标，则返回`true`。例如，`hasPermission(domainObject, 'read')` |
| `hasPermission(Object targetId, String targetType, Object permission)` | 如果用户有权访问给定权限的提供目标，则返回`true`。例如，`hasPermission(1, 'com.example.domain.Message', 'read')` |

