# Struts2

- 三大框架：Struts2、Hibernate、Spring

2. 三层架构与Struts2
   1. 表现层：M V C
   2. 业务层：Service
   3. 持久层：dao  data access object
3. MVC 与 Struts2
   - 控制器：Servlet 
     - init() 
     - destroy()
     - service()
   - 过滤器：Filter 它也可以作为控制
     - init(FilterConfig)
     - destroy()
     - doFilter()

### Struts2开发步骤

1. 引入Jar文件

![image-20200722172209477](C:\Users\ChinaLi\Desktop\note\JavaNote\struts2\images\image-20200722172209477.png)

2. 配置web.xml

   - 在web.xml中配置的过滤器，就是为struts进行初始化工作的
   - 注意<b>如果该web.xml配置了多个filter，那么struts的filter需要在最后面</b>

   ```xml
   <!-- 引入struts核心过滤器 -->
       <filter>
           <filter-name>struts2</filter-name>
           <filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
       </filter>
       <filter-mapping>
           <filter-name>struts2</filter-name>
           <url-pattern>/*</url-pattern>
       </filter-mapping>
   ```

   3. 开发Action

   - Servlet的业务代码，我们都使用Action来代替，Action类一般都继承着ActionSupport
   - Action类也叫动作类，处理请求的类

   4. 配置struts.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
           <!DOCTYPE struts PUBLIC
                   "-//Apache Software Foundation//DTD Struts Configuration 2.0//EN"
                   "http://struts.apache.org/dtds/struts-2.0.dtd">
   <struts>
   <package name="hello" extends="struts-default">
       <action name="hello" class="action.HelloAction" method="execute">
           <result name="success">/index.jsp</result>
   
       </action>
   </package>
   </struts>
   ```

   

### Struts2的执行过程

> 客户浏览器  --->  Tomcat服务器 --->  web.xml --->  struts.xml --->  HelloAction 

#### 服务器启动

- 加载web.xml文件
- 找到我们配置的filter中的StrutsPrepareAndExecuteFilter
- StrutsPrepareAndExecuteFilter在里面执行init()方法
- 一直到Dispatcher dispatcher = init.initDispatcher(config);初始siapatcher
- 在初始化dispatcher的时候加载struts-default.xml和我们配置的struts.xml

#### 访问阶段

- 在服务启动的阶段，仅仅是加载了各种的xml.文件，那么当我们访问Action的时候，它的执行流程
- 首先，它会创建我们在struts.xml中配置的Action对象
- 接着，它会按照默认的顺序执行18个拦截器【也就是调用默认拦截器】
- 最后，它会执行Action的业务方法
- <b>没访问Action一次，他就会创建一个对象，并不是和Servlet一样只有一个对象，因此它是线程安全的</b>

### Struts2配置文件

- 加载时机：服务器启动的时候就已经加载好了
- 加载顺序：default.properties -- struts-default.xml -- plugins配置 -- struts.xml -- struts.properties -- web.xml
- 如果遇到重名，后一个覆盖前一个

### Struts2中的常量

- Struts2默认的访问后缀是 ： .action 

  - 修改默认访问路径：在struts.xml文件中提供了constant节点供我们修改struts常量

    ><constrant name="struts.action.extension" value="action, ,"

    

- struts.devMode: 开发模式，系统可以打印出更详细的错误信息
- 指定编码集，作用于HttpServletRequest的setCharacterEncoding方法和freemarker、velocity的输出：<constant name="struts.i18n.encoding" value="UTF-8"/>
- 与spring集成时，指定由spring负责action对象的创建<constant name="struts.objectFactory" value="spring" />

### Struts2配置文件详解

- package元素：用来管理Action
  - name属性：包的名字，包的名字是不能重复的
  - extends属性：代表当前包继承着那个包。在struts中，包一定要继承着struts-default
  - abstract属性: 表明当前包被其他的包继承，并且在package下不能有action
  - namespace属性：如何查找名称空间和动作类，名称空间，作为路径的一部分
- action元素：配置请求路径与Action类的映射关系
  - name属性：请求路径的名字
  - class属性：处理action类的全名
  - method属性：调用的方法名称

### Struts2中结果视图详解

- result元素
  - name属性：如果不写name属性，默认值是success
  - type属性：以何种方式前往指定的视图或者动作。默认值是dispatcher
- 自定义结果视图
  - 步骤：
    1. 创建类，继承StrutsResultSupport,重写doExecute的方法
    2. 在struts.xml中配置
- 全局视图和局部视图

### Struts2中调用ServletAPI

	1. 使用ServletActionContext
 	2. 采用注入的方式，实现接口

### 分文件编写struts2的配置文件



### 通配符

- 产生原因：仅仅只有访问路径和方法的名称是不一样的，却要配置一个action节点，会造成浪费

#### 使用通配符改造

- \* 就是我们的通配符
- {1} , 就是代表着第一个通配符

### Struts中路径的匹配原则

- 有的时候，我们可能会在package节点中指定namespace名称空间，我们在访问对应资源名称的时候，就需要在前面加入相对应名称空间的值

- 随便举个例子：http://localhost:8080/user/a/a/privilege_login

- 首先，Struts会把请求的资源路径拿到，也就是privilege_login
- 然后在看看有没有 <b>/user/a/a</b>这个名称空间；如果有，就判断有没有这个资源路径
- 如果没有，就看看有没有<b>/user/a</b>这个名称空间，如果有就返回结果
- 如果没有，再看看有没有/user 这个名称空间，如果有就返回结果
- 直到最后，如果都没有找到。就报错 

### 跳转全局视图

#### 为什么需要跳转全局视图

- 只要方法返回值是success, 那么就跳转到首页，如果有大量Action方法的返回都是success, 那么就要写很多很多个result节点了

#### 使用跳转全局视图

- global-results 节点需要在action节点的上面

> <package name="privilige" extends="struts-default" >
>
>    <global-results>
>      <result name="success">/index.jsp</result>
>    </global-results>
>
>    <action name="privilege_*" class="privilegeaction.PrivilegeAction" method="{1}">
>    </action>
>
>    <action name="category_*" class="privilegeaction.CategoryAction" method="{1}">
>    </action>
> </package>

### Action节点中的默认配置

#### method

- 如果我们不写method的话，默认执行的是execute()方法，execute()方法默认返回值是SUCCESS

#### class

- 如果不写class, class默认执行的action在struts-default有配置<default-class-ref class="com.opensymphony.xwork2.ActionSupport" />
- 什么时候会不写class ? 就是需要跳转到WEB-INF目录下的资源的时候，这就类似与Servlet需要跳转到WEB-INF下的资源【Tomcat不允许直接访问WEB-INF资源】

### Action开发的三种方式

#### 继承ActionSupport类



- 我们在Action类中需要用到Struts为我们提供的数据校验等Struts已经帮我们实现的功能，我们就继承着ActionSupport
- 最常用

#### 实现Action接口

- ActionSupport也继承着Action接口，所以ActionSupport拥有Action接口的全部功能

#### 不继承任何类、不实现任何接口

### 请求数据封装

- 我们自己编写的Action类是没有request、response、Session、application之类的对象的。我们如何得到web层的数据，再将数据存到域对象中的
- Struts预先帮我们完成了对数据封装的功能，它是通过params拦截器来实现数据封装的

> <interceptor name="params" class="com.opensymphony.xwork2.interceptor.ParametersInterceptor"/>

#### Action封装基本信息

- 在Action设置与JSP页面相同的属性，并为它们编写setter方法

```java
private String username;
private String psd;

public void setUsername(String username) {
    this.username = username;
}

public void setPsd(String psd) {
    this.psd = psd;
}
```

#### Action封装对象

- 创建一个User类，基本的信息和JSP页面都是相同的
- 在Action中定义User对象出来，并给出setter和getter方法，值得注意的是：基本信息只要setter就够了，封装到对象的话，需要setter和getter
- 在JSP页面，提交的name要写成user.username之类的

### 得到域对象

#### 一、得到ServletAPI

- 通过ServletActionContext得到ServletAPI
- 由于每个用户拥有一个Action对象，那么底层为了维护用户拿到的是当前线程的request等对象，使用ThreadLocal来维护当前线程下的request、response等对象

```java
//通过ServletActionContext得到Servlet API
        javax.servlet.ServletContext context = ServletActionContext.getServletContext();
        HttpServletRequest request = ServletActionContext.getRequest();
        HttpSession session = request.getSession();
        HttpServletResponse response = ServletActionContext.getResponse();
```

#### 二、ActionContext类

- 我们还可以通过ActionContext类来得到request、response、session、application被Struts封装的Map集合

```java
//得到ActionContext 对象
        ActionContext context = ActionContext.getContext();
        Map<String, Object> session = context.getSession();
        Map<String, Object> application = context.getApplication();

        //这是request的Map
        Map<String, Object> request = context.getContextMap();
```

#### 三、实现接口

- 当web容器发现该Action实现了Aware接口，会把相对应的资源通过Aware接口注射进去，实际就是一种IOC
- Aware实际就是一种拦截器，拦截代码在执行Action之前执行，将资源注射到Action中
- 实现SessionAware、RequestAware、ApplicationAware接口，它就要在程序中实现三个方法

## Struts2拦截器

### 什么是拦截器

- 拦截器Interceptor，拦截器是Struts的概念，与过滤器类似
- 拦截器的设计就是基于组件设计的应用

### 拦截器基础

- struts-default.xml文件中定义了Struts的所有拦截器，我们阿紫启动服务器的时候会自动装在这个文件，因此我们才可以在Action中使用到Struts为我们提供的功能
- 在struts-default.xml中定义的拦截器就有32个之多，Struts2为了方便我们对拦截器的引用，提供了拦截器栈的定义
- 当我们要引用多个拦截器的时候，只要把拦截器都放在栈里头，在外边引用拦截器即可
- Struts2默认执行的是默认拦截器栈，一旦用户指定执行那些拦截器，那么默认的拦截器栈就不会被执行

### 自定义拦截器

