```javascript
http.authorizeRequests()
        .antMatchers("/admin/**").hasAuthority("admin")
        .antMatchers("/user/**").hasAuthority("user")
        .anyRequest().authenticated()
```

以及

```javascript
http.authorizeRequests()
        .antMatchers("/admin/**").hasRole("admin")
        .antMatchers("/user/**").hasRole("user")
        .anyRequest().authenticated()
```

那么这两种配置有什么区别呢？

## **1.源码分析**

单纯从源码上来分析，你会发现这两个东西似乎一样，先来看 hasAuthority。

```javascript
public ExpressionInterceptUrlRegistry hasAuthority(String authority) {
 return access(ExpressionUrlAuthorizationConfigurer.hasAuthority(authority));
}
private static String hasAuthority(String authority) {
 return "hasAuthority('" + authority + "')";
}
```

最终调用了 access 方法，传入了权限表达式 hasAuthority('xxx')。

再看 hasRole：

```javascript
public ExpressionInterceptUrlRegistry hasRole(String role) {
 return access(ExpressionUrlAuthorizationConfigurer.hasRole(role));
}
private static String hasRole(String role) {
 Assert.notNull(role, "role cannot be null");
 if (role.startsWith("ROLE_")) {
  throw new IllegalArgumentException(
    "role should not start with 'ROLE_' since it is automatically inserted. Got '"
      + role + "'");
 }
 return "hasRole('ROLE_" + role + "')";
}
```

可以看到，hasRole 的处理逻辑和 hasAuthority 似乎一模一样，不同的是，hasRole 这里会自动给传入的字符串加上 `ROLE_` 前缀，所以在[数据库](https://cloud.tencent.com/solution/database?from=10680)中的权限字符串需要加上 `ROLE_` 前缀。即数据库中存储的用户角色如果是 `ROLE_admin`，这里就是 admin。

我们在调用 `hasAuthority` 方法时，如果数据是从数据库中查询出来的，这里的权限和数据库中保存一致即可，可以不加 `ROLE_` 前缀。即数据库中存储的用户角色如果是 admin，这里就是 admin。

**也就是说，使用 `hasAuthority` 更具有一致性，你不用考虑要不要加 `ROLE_` 前缀，数据库什么样这里就是什么样！而 `hasRole` 则不同，代码里如果写的是 `admin`，框架会自动加上 `ROLE_` 前缀，所以数据库就必须是 `ROLE_admin`。**

看起来 hasAuthority 和 hasRole 的区别似乎仅仅在于有没有 `ROLE_` 前缀。

在最终的权限比对中，更是过分，hasAuthority 和 hasRole 居然最终都是调用了 hasAnyAuthorityName 方法（SecurityExpressionRoot 类）：

```javascript
public final boolean hasAuthority(String authority) {
 return hasAnyAuthority(authority);
}
public final boolean hasAnyAuthority(String... authorities) {
 return hasAnyAuthorityName(null, authorities);
}
public final boolean hasRole(String role) {
 return hasAnyRole(role);
}
public final boolean hasAnyRole(String... roles) {
 return hasAnyAuthorityName(defaultRolePrefix, roles);
}
private boolean hasAnyAuthorityName(String prefix, String... roles) {
 Set<String> roleSet = getAuthoritySet();
 for (String role : roles) {
  String defaultedRole = getRoleWithDefaultPrefix(prefix, role);
  if (roleSet.contains(defaultedRole)) {
   return true;
  }
 }
 return false;
}
```

hasAnyRole 在调用 hasAnyAuthorityName 方法时设置了 `ROLE_` 前缀，hasAnyAuthority 在调用 hasAnyAuthorityName 方法时没有设置前缀。

所以我们单纯从源码角度来看，`hasRole` 和 `hasAuthority` 这两个功能似乎一模一样，除了前缀之外就没什么区别了。

那么 Spring Security 设计者为什么要搞两个看起来一模一样的东西呢？

## **2.设计理念**

从设计上来说，这是两个不同的东西。同时提供 role 和 authority 就是为了方便开发者从两个不同的维度去设计权限，所以并不冲突。

authority 描述的的是一个具体的权限，例如针对某一项数据的查询或者删除权限，它是一个 permission，例如 read_employee、delete_employee、update_employee 之类的，这些都是具体的权限，相信大家都能理解。

role 则是一个 permission 的集合，它的命名约定就是以 `ROLE_` 开始，例如我们定义的 ROLE 是 `ROLE_ADMIN`、`ROLE_USER` 等等。我们在 Spring Security 中的很多地方都能看到对 Role 的特殊处理，例如上篇文章我们所讲的[投票器和决策器中](https://mp.weixin.qq.com/s?__biz=MzI1NDY0MTkzNQ==&mid=2247490093&idx=1&sn=0fc060f0dc3e00035517edce5fb4113f&scene=21#wechat_redirect)，RoleVoter 在处理 Role 时会自动添加 `ROLE_` 前缀。

在项目中，我们可以将用户和角色关联，角色和权限关联，权限和资源关联。

反映到代码上，就是下面这样：

假设用 Spring Security 提供的 SimpleGrantedAuthority 的代表 authority，然后我们自定义一个 Role，如下：

```javascript
public class Role implements GrantedAuthority {
    private String name;

    private List<SimpleGrantedAuthority> allowedOperations = new ArrayList<>();

    @Override
    public String getAuthority() {
        return name;
    }

    public List<SimpleGrantedAuthority> getAllowedOperations() {
        return allowedOperations;
    }

    public void setAllowedOperations(List<SimpleGrantedAuthority> allowedOperations) {
        this.allowedOperations = allowedOperations;
    }
}
```

一个 Role 就是某些 authority 的集合，然后在 User 中定义 roles 集合。

```javascript
public class User implements UserDetails {
    private List<Role> roles = new ArrayList<>();

    public List<Role> getRoles() {
        return roles;
    }

    public void setRoles(List<Role> roles) {
        this.roles = roles;
    }

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        List<SimpleGrantedAuthority> authorities = new ArrayList<>();
        for (Role role : roles) {
            authorities.addAll(role.getAllowedOperations());
        }
        return authorities.stream().distinct().collect(Collectors.toList());
    }
}
```

在 getAuthorities 方法中，加载 roles 中的权限去重后再返回即可。

通过这个例子大家应该就能搞明白 Role 和 Authority 了。

松哥在 Spring Security 的 issue 上也看到了一个类似的问题：https://github.com/spring-projects/spring-security/issues/4912

从作者对这个问题的回复中，也能看到一些端倪：

1. 作者承认了目前加 `ROLE_` 前缀的方式一定程度上给开发者带来了困惑，但这是一个历史积累问题。
2. 作者说如果不喜欢 `ROLE_`，那么可以直接使用 `hasAuthority` 代替 `hasRole`，言下之意，就是这两个功能是一样的。
3. 作者还说了一些关于权限问题的看法，权限是典型的对对象的控制，但是 Spring Security 开发者不能向 Spring Security 用户添加所有权限，因为在大多数系统中，权限都过于复杂庞大而无法完全包含在内存中。当然，如果开发者有需要，可以自定义类继承自 GrantedAuthority 以扩展其功能。

从作者的回复中我们也可以看出来，`hasAuthority` 和 `hasRole` 功能上没什么区别，设计层面上确实是两个不同的东西。

## **3.历史沿革**

实际上，在 Spring Security4 之前，`hasAuthority` 和 `hasRole` 几乎是一模一样的，连 `ROLE_` 区别都没有！

即 `hasRole("admin")` 和 `hasAuthority("admin")` 是一样的。

而在 Spring Security4 之后，才有了前缀 `ROLE_` 的区别。

这块如果小伙伴们感兴趣的话，可以看看 Spring Security3 到 Spring Security4 的迁移文档：

- http://docs.spring.io/spring-security/site/migrate/current/3-to-4/html5/migrate-3-to-4-jc.html#m3to4-role-prefixing

## **4.小结**

一言以蔽之，代码上来说，hasRole 和 hasAuthority 写代码时前缀不同，但是最终执行是一样的；设计上来说，role 和 authority 这是两个层面的权限设计思路，一个是角色，一个是权限，角色是权限的集合。