# 2013-05-27-spring-web-mvc

用了这么久的spring web，终于把官方的文档看了看，总结出来下面的文档。

<!-- more -->

####Spring WEB MVC的特性

* 角色分明：controller、validator、command object、form object、model object、DispatcherServlet、handler mapping、view resolver等等，每个角色都可以由一个专门的对象来实现；
* 框架和类似JavaBean应用类的强大而简单的配置；
* 适配性、非侵入性以及灵活性。可以任意定义controller方法的前面，对于指定的场景，可以使用@RequestParam、@RequestHeader、@PathVariable其中的一个；
* 可重用的业务代码；
* 可定制话的绑定和验证；
* 类型匹配错误作为应用层的验证错误，取代手动将传进来的String-Only对象parse and convert成为业务对象；
* 个性化handler mapping以及view resolution；
* 灵活的model解析。利用name/value Map，可以利用任意一种view技术解析model；
* 个性化的locale以及theme resolution。支持JSPs，包不包含Spring tag lib，JSTL都行；
* 等等

####其他MVC实现的可插入性

如果不想使用Spring web MVC，而是想在其他类似struts、WebWork的框架中使用Spring的某些特性，这也是可以的，具体的可以查看相关文档。

####1. DispatcherServlet类

和其他的MVC框架一样，Spring web MVC也是基于__请求驱动__的模型，围绕一个核心的servlet，将所有的请求分发到Controllers，同时提供丰富的功能，来帮助web应用程序的开发。Spring完全集成Spring IoC容器，允许你使用Spring所拥有的任何特性。

下图是Spring web MVC中的请求处理流程图：

{% img https://dl.dropboxusercontent.com/u/99113526/blog.bd17kaka.net/spring-web-mvc.png %}

DispatcherServlet是Servlet类的一个子类，继承自HttpServlet，它在web.xml文件中进行定义。需要通过使用URL映射，来将需要其处理的请求映射到这个Servlet。下面是一个标准的servlet配置：

```
<!-- web.xml -->
<web-app>

    <servlet>
        <servlet-name>example</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>example</servlet-name>
        <url-pattern>*.form</url-pattern>
    </servlet-mapping>

</web-app>
```

额外的参数：contextClass、contextConfigLocation、namespace，具体解释见原文。

在这个例子中，所有满足表达式“*.form”的请求，都将有servlet-name为“example”的这个servlet来进行处理。

在Spring中，ApplicationContext实例是有作用域的。在Web MVC架构中，每一个DispatcherServlet类都有它自己的WebApplicationContext，这个WebApplicationContext继承所有已经在根部的WebApplicationContext中所定义的beans。这些beans是可以被在servlet中定义的beans所覆盖的。（不太懂，得看看Spring Ioc）

在DispatcherServlet初始化的时候，Spring在文件夹WEB-INF中查找文件名为[servlet-name]-servlet.xml的文件，并且创建那个文件里面定义的所有beans。将在全局作用域中，有相同名称的bean全覆盖掉。

考虑上面的那个web.xml定义，那么需要在WEB-INF文件夹中，创建一个名为example-servlet.xml的文件，这个里面包含所有Spring WEB MVC所拥有的beans。

__我的理解是：一个web.xml文件中可以定义多个servlet；一个servlet对应一个example-servlet.xml文件；一个example-servlet.xml文件就是一个WebApplicationContext。__

在WebApplicationContext中，包含下面这些类型的beans：

* controllers
* handler mappings
* view resolvers
* locale resolvers
* theme resolver
* multipart file resolver
* handler exception resolvers

当有一个请求到来的时候，DispatcherServlet是按照以下的顺序来处理这个请求的：

* 搜索WebApplicationContext，并且将其作为一个attribute绑定到request中，这样，在这个过程中，controller和其他的元素都可以使用。默认情况下，它被绑定在DispatcherServlet.WEB_APPLICATION_CONTEXT_ATTRIBUTE里面；
* locale resolver绑定到request中，这样，其他的元素可以解析并使用locale（呈现view、准备数据等等）。如果你不需要locale resolving，那么就不需要他它；
* theme resolver绑定到request中，允许views等元素决定使用哪个theme。如果不需要themes，可以忽略它；
* 如果指定了一个multipart file resolver，那么这个请求就被看做multipart。如果找到了multipart，就会将这个请求包装成一个MultipartHttpServletRequest，再由其它元素进行进一步的处理；
* 搜索一个合适的handler。如果找到了，那个和这个handler相关的执行链（preprocessors, postprocessors, and controllers）会被执行，以准备model以及呈现；
* 如果返回了一个model，那么会呈现一个view；如果没有model返回，那么不会呈现任何view，因为请求以及被执行完毕了。

在WebApplicationContext中声明的Handler exception resolvers对象，接收在请求处理过程中产生的所有错误。用这些exception resolvers可以使对于不同的exception定义个性化的处理办法。

####2. Controllers

controllers获取用户的输入，并且在处理之后，将其转换为model，进而转换为view，展现给用户。Spring提供的controller是非常抽象化的，可以定义各种各样、大量的controllers。

Spring2.5引入基于注解的编程模型，可以使用诸如@RequestMapping、@RequestParam、@ModelAttribute等注解。它同时支持Servlet MVC和Potlet MVC。这种情况下，不需要事先指定的基类或者指定的接口。下面是一个简单的例子：

```
@Controller
public class HelloWorldController {

    @RequestMapping("/helloWorld")
    public ModelAndView helloWorld() {
        ModelAndView mav = new ModelAndView();
        mav.setViewName("helloWorld");
        mav.addObject("message", "Hello World!");
        return mav;
    }
}

```

#####2.1 使用@controller定义一个controller

@controller标签定义一个执行controller功能的类，在Spring里面，不需要继承任何的controller基类，或者调用ServletAPI，不过，如果有需要的话，也可以调用Servlet指定的特性。

@controller作为被注解的类的签名，标示这个类的角色。Dispatcher类扫描以@controller做注解的类，然后寻找以@RequestMapping做注解的方法。

我们可以严格的对controller类使用@controller注解，但是@controller标签页支持自动搜索，依赖于Spring支持在classpath中自动搜索组件类，以及为他们完成自动注册的bean的定义。

只需要将扫描组件添加到配置中，即可启用自动扫描这样的注解controller的功能：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p" 
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
        http://www.springframework.org/schema/context 
        http://www.springframework.org/schema/context/spring-context-3.0.xsd">

    <context:component-scan base-package="org.springframework.samples.petclinic.web"/>

    // ...

</beans>
```

#####2.2 利用@RequestMapping来匹配请求

我们可以用类似于“/handler”的注解来匹配整个类，也可以只匹配某个函数。比如，对于一个指定的HTTP请求（GET/POST）或者一个HTTP请求参数，我们经常用一个__类级别__的注解来将一个指定的请求映射到一个表单controller，然后使用一个额外的__方法级别__的注解来缩小范围。下面是一个例子：

```
@Controller
@RequestMapping("/handler")
public class AppointmentsController {

    private final AppointmentBook appointmentBook;
    
    @Autowired
    public AppointmentsController(AppointmentBook appointmentBook) {
        this.appointmentBook = appointmentBook;
    }

    @RequestMapping(method = RequestMethod.GET)
    public Map<String, Appointment> get() {
        return appointmentBook.getAppointmentsForToday();
    }

    @RequestMapping(value="/{day}", method = RequestMethod.GET)
    public Map<String, Appointment> getForDay(@PathVariable @DateTimeFormat(iso=ISO.DATE) Date day, Model model) {
        return appointmentBook.getAppointmentsForDay(day);
    }

    @RequestMapping(value="/new", method = RequestMethod.GET)
    public AppointmentForm getNewForm() {
        return new AppointmentForm();
    }

    @RequestMapping(method = RequestMethod.POST)
    public String add(@Valid AppointmentForm appointment, BindingResult result) {
        if (result.hasErrors()) {
            return "appointments/new";
        }
        appointmentBook.addAppointment(appointment);
        return "redirect:/appointments";
    }
}
```

类级别的@RequestMapping注解并不是必须的，没有它的话，所有路径都是绝对路径，而不是相对路径。

这里有一个当使用proxy时，关于@controller注解类的小陷阱，具体内容见原文。

######2.2.1 URI模板

如果想要访问URL中的某个部分，那么就可以在@RequestMapping的路径变量中使用URL模板。

在函数中使用@PathVariable注解时，意味着函数的这个变量需要绑定到URI模板变量的某个部分。

例如：

```
@RequestMapping(value="/owners/__{ownerId}__", method=RequestMethod.GET)
public String findOwner(@PathVariable String ownerId, Model model) {
  Owner owner = ownerService.findOwner(ownerId);  
  model.addAttribute("owner", owner);  
  return "displayOwner"; 
}
```

如果代码编译是在启用debugging模式下进行的，那么路径中的ownerId的值会被设置到函数变量ownerId中；如果没有启用debugging模式，那么必须给函数变量指定它要映射到的路径变量的名称，例如：

```
@RequestMapping(value="/owners/{ownerId}", method=RequestMethod.GET)
public String findOwner(__@PathVariable("ownerId")__ String ownerId, Model model) {
  // implementation omitted
}

```

也就是说，在没有启用debugging模式的时候，路径中的变量是可以和函数声明中的变量按名称来进行匹对，进而赋值的；在没有启用debugging模式的时候，必须要指定与函数签名中的变量进行匹对的路径中的变量的名称。所以在没有启用debugging的时候，下面的写法也是而已的：

```
@RequestMapping(value="/owners/{ownerId}", method=RequestMethod.GET)
public String findOwner(@PathVariable("ownerId") String __theOwner__, Model model) {
  // implementation omitted
}
```

当然也可以指定多个路径变量：

```
@RequestMapping(value="__/owners/{ownerId}/pets/{petId}__", method=RequestMethod.GET)
public String findPet(@PathVariable String ownerId, @PathVariable String petId, Model model) {
  Owner owner = ownerService.findOwner(ownderId);  
  Pet pet = owner.getPet(petId);  
  model.addAttribute("pet", pet);  
  return "displayPet"; 
}
```

下面的实例演示了相对路径的用法：

```
@Controller
@RequestMapping("/owners/{ownerId}")
public class RelativePathUriTemplateController {

  @RequestMapping("/pets/{petId}")
  public void findPet(@PathVariable String ownerId, @PathVariable String petId, Model model) {    
    // implementation omitted
  }
}

```

__注意：@PathVariable中声明的变量可以是任意类型的，Spring会进行自动的类型转换，如果转换失败的话，会抛出TypeMismatchException异常。我们也可以通过定制data binder来定制这个转换的过程，具体见后面。__

######2.2.2 @RequestMapping的更多选项

除了URI模板之外，@RequestMapping同样支持正则，比如`/myPath/*.do`，URI路径和正则的组合也是支持的（`/owners/*/pets/{petId}`）。

如果没有严格匹配的方法来处理请求，那么处理方法的名称用于缩小范围。如果有多个这样的方法，那么就会在这些方法里面进行选择。

可以通过类似于`myParam=myValue`的参数条件来缩小路径匹配的范围。比如：

```
@Controller
@RequestMapping("/owners/{ownerId}")
public class RelativePathUriTemplateController {

  @RequestMapping(value = "/pets/{petId}", __params="myParam=myValue"__)
  public void findPet(@PathVariable String ownerId, @PathVariable String petId, Model model) {    
    // implementation omitted
  }

}
```

“myParam”这种形式的表达式也是允许的，意思是参数myParam存在于请求路径中，同样的，“！myParam”的意思是参数myParam不存在与请求路径中。

相似的，也可以通过比较header的信息来缩小路径匹配的范围：

```
@Controller
@RequestMapping("/owners/{ownerId}")
public class RelativePathUriTemplateController {

@RequestMapping(value = "/pets", method = RequestMethod.POST, __headers="content-type=text/*"__)
  public void addPet(Pet pet, @PathVariable String ownerId) {    
    // implementation omitted
  }
}
```

######2.2.3 处理方法支持的参数和返回值类型

被@RequestMapping注解的处理方法可以有非常灵活的签名，他们中的很多都可以以任意的顺序所使用。

* Request和Response对象（Servlet API）：选择任意指定的request或者response类型，比如ServletRequest和HttpServletRequest；
* 

