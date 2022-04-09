@AuthenticationPrincipal 注解

```java
/**
 * Annotation that is used to resolve {@link Authentication#getPrincipal()} to a method
 * argument.
 *
 * @author Rob Winch
 * @since 4.0
 *
 * See: <a href=
 * "{@docRoot}/org/springframework/security/web/method/annotation/AuthenticationPrincipalArgumentResolver.html"
 * > AuthenticationPrincipalArgumentResolver </a>
 */
@Target({ ElementType.PARAMETER, ElementType.ANNOTATION_TYPE })
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface AuthenticationPrincipal {

	/**
	 * True if a {@link ClassCastException} should be thrown when the current
	 * {@link Authentication#getPrincipal()} is the incorrect type. Default is false.
	 *
	 * @return
	 */
	boolean errorOnInvalidType() default false;

	/**
	 * If specified will use the provided SpEL expression to resolve the principal. This
	 * is convenient if users need to transform the result.
	 *
	 * <p>
	 * For example, perhaps the user wants to resolve a CustomUser object that is final
	 * and is leveraging a UserDetailsService. This can be handled by returning an object
	 * that looks like:
	 * </p>
	 *
	 * <pre>
	 * public class CustomUserUserDetails extends User {
	 *     // ...
	 *     public CustomUser getCustomUser() {
	 *         return customUser;
	 *     }
	 * }
	 * </pre>
	 *
	 * Then the user can specify an annotation that looks like:
	 *
	 * <pre>
	 * &#64;AuthenticationPrincipal(expression = "customUser")
	 * </pre>
	 *
	 * @return the expression to use.
	 */
	String expression() default "";
}
```

