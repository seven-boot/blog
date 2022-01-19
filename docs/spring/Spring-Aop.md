## 在aop中execution的写法规则

执行表达式的格式如下： 
execution(modifiers-pattern? ret-type-pattern declaring-type-pattern? name-pattern(param-pattern) throws-pattern?) 
除了返回类型模式（上面代码片断中的ret-type-pattern），名字模式和参数模式以外，所有的部分都是可选的。 返回类型模式决定了方法的返回类型必须依次匹配一个连接点。 你会使用的最频繁的返回类型模式是 * ，它代表了匹配任意的返回类型。 一个全称限定的类型名将只会匹配返回给定类型的方法。名字模式匹配的是方法名。 你可以使用 * 通配符作为所有或者部分命名模式。 参数模式稍微有点复杂：() 匹配了一个不接受任何参数的方法， 而 (..) 匹配了一个接受任意数量参数的方法（零或者更多）。 模式 (*) 匹配了一个接受一个任何类型的参数的方法。 模式 (*,String) 匹配了一个接受两个参数的方法，第一个可以是任意类型，第二个则必须是String类型。 
一些常见切入点表达式的例子。 

execution(public * *(..))　　任意公共方法的执行;
execution(* set*(..))　　任何一个以“set”开始的方法的执行; 
execution(* com.xyz.service.AccountService.*(..)) 　AccountService接口的任意方法的执行;
execution(* com.xyz.service.*.*(..))　　定义在service包里的任意方法的执行;
execution(* com.xyz.service..*.*(..))　　定义在service包或者子包里的任意方法的执行;