- Spring MVC 将所有的请求都提交给 `DispatcherServlet`，它会委托应用系统的其他模块负责对请求进行真正的处理工作。
- `DispatcherServlet` 查询一个或多个 `HandlerMapping`，找到处理请求的 Controller.
- `DispatcherServlet` 请求提交到目标 Controller
- Controller 进行业务逻辑处理后，会返回一个 `ModelAndView`
- Dispatcher 查询一个或多个 `ViewResolver` 视图解析器,找到 `ModelAndView` 对象指定的视图对象
- 视图对象负责渲染返回给客户端。

### SpringMVC 架构模式：

#### 组件说明：

以下组件通常使用框架提供实现：

DispatcherServlet：作为前端控制器，整个流程控制的中心，控制其它组件执行，统一调度，降低组件之间的耦合性，提高每个组件的扩展性。

HandlerMapping：通过扩展处理器映射器实现不同的映射方式，例如：配置文件方式，实现接口方式，注解方式等。 

HandlAdapter：通过扩展处理器适配器，支持更多类型的处理器。

ViewResolver：通过扩展视图解析器，支持更多类型的视图解析，例如：jsp、freemarker、pdf、excel等。

**1、前端控制器DispatcherServlet（不需要工程师开发）,由框架提供**
作用：接收请求，响应结果，相当于转发器，中央处理器。有了dispatcherServlet减少了其它组件之间的耦合度。
用户请求到达前端控制器，它就相当于mvc模式中的c，dispatcherServlet是整个流程控制的中心，由它调用其它组件处理用户的请求，dispatcherServlet的存在降低了组件之间的耦合性。

**2、处理器映射器HandlerMapping(不需要工程师开发),由框架提供**
作用：根据请求的url查找Handler
HandlerMapping负责根据用户请求找到Handler即处理器，springmvc提供了不同的映射器实现不同的映射方式，例如：配置文件方式，实现接口方式，注解方式等。

**3、处理器适配器HandlerAdapter**
作用：按照特定规则（HandlerAdapter要求的规则）去执行Handler
通过HandlerAdapter对处理器进行执行，这是适配器模式的应用，通过扩展适配器可以对更多类型的处理器进行执行。

**4、处理器Handler(需要工程师开发)**
**注意：编写Handler时按照HandlerAdapter的要求去做，这样适配器才可以去正确执行Handler**
Handler 是继DispatcherServlet前端控制器的后端控制器，在DispatcherServlet的控制下Handler对具体的用户请求进行处理。
由于Handler涉及到具体的用户业务请求，所以一般情况需要工程师根据业务需求开发Handler。

**5、视图解析器View resolver(不需要工程师开发),由框架提供**
作用：进行视图解析，根据逻辑视图名解析成真正的视图（view）
View Resolver负责将处理结果生成View视图，View Resolver首先根据逻辑视图名解析成物理视图名即具体的页面地址，再生成View视图对象，最后对View进行渲染将处理结果通过页面展示给用户。 springmvc框架提供了很多的View视图类型，包括：jstlView、freemarkerView、pdfView等。
一般情况下需要通过页面标签或页面模版技术将模型数据通过页面展示给用户，需要由工程师根据业务需求开发具体的页面。

**6、视图View(需要工程师开发jsp...)**
View是一个接口，实现类支持不同的View类型（jsp、freemarker、pdf...）

![9824247-5b7f438fcfd51ce2](assets/9824247-5b7f438fcfd51ce2.webp)

一个典型的SpringMVC请求流程如图所示，详细分为12个步骤：

1. 用户发起请求，由前端控制器DispatcherServlet处理
2. 前端控制器通过处理器映射器查找hander，可以根据XML或者注解去找
3. 处理器映射器返回执行链
4. 前端控制器请求处理器适配器来执行hander
5. 处理器适配器来执行handler
6. 处理业务完成后，会给处理器适配器返回ModeAndView对象，其中有视图名称，模型数据
7. 处理器适配器将视图名称和模型数据返回到前端控制器
8. 前端控制器通过视图解析器来对视图进行解析
9. 视图解析器返回真正的视图给前端控制器
10. 前端控制器通过返回的视图和数据进行渲染
11. 返回渲染完成的视图
12. 将最终的视图返回给用户，产生响应