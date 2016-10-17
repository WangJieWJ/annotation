# annotation
Spring中常用注解的用法总结

## Spring Boot
    Spring早期使用XML配置的方式来配置Spring Beans之间的关系，比如AOP和依赖注入的配置。随着功能以及业务逻辑的日益复杂，
    应用便会伴随大量的XML配置文件以及复杂的Bean依赖关系。随着Spring 3.0的发布，Spring团队逐渐开始摆脱XML配置文件，
    并且在开发过程中大量使用“约定优先配置”（convention over configuration）的思想来摆脱Spring框架中各类纷繁复杂的配置。
    Spring就是在这样一种背景下被抽象出来的一个在Spring之上的开发框架。

    Spring Boot的设计目的是用来简化新Spring应用的创建以及开发过程。从它的名字可以看出，其作用在于创建和启动新的基于 Spring 框架的项目，
    它能够帮助开发人员很容易的创建出基于Spring的独立运行和产品级别的应用。
### Spring Boot具有以下特性： 
    1、应用独立运行，对于Web应用直接嵌入应用服务器（Tomcat or Jetty）
    2、根据项目的依赖（Maven or Gradle中定义的依赖）自动配置Spring框架的特性
    3、提供生产环境中的监控功能——性能、应用状态信息等
    4、不会生成繁琐的Java代码以及XML配置文件

### 总结：
    Spring Boot并不是用来替代Spring的解决方案，而是和Spring框架紧密结合用于提升Spring开发者体验的工具。
    Spring Boot应用通常只需要非常少量的配置代码，而且有内嵌的Web服务器，让开发者能够更加专注于业务逻辑

### 编写一个类包含处理HTTP请求的方法以及一个main()函数
```java
package com.tianmaying;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@Controller
public class TblogApplication {

    @RequestMapping("/")
    @ResponseBody
    String greeting() {
        return "Hello World";
    }

    public static void main(String[] args) {
        SpringApplication.run(TblogApplication.class, args);
    }
}
```
### 有两种方法可以启动应用：
    1、在IDE（或者命令行工具中的java）启动main函数，IDE中一般都自带Maven，能够帮助我们下载安装Maven依赖。
    2、在命令行工具中运行mvn spring-boot:run，但是此种方法要求你在本地环境中必须安装Maven
    
    在控制台中可以发现启动了一个Tomcat容器，一个基于Spring MVC的应用也同时启动起来，
    这时访问http://localhost:8080就可以看到Hello World!出现在浏览器中了。

### 代码详解
    上述代码就是一个Java Web项目的骨架，在接下来的课程中我们会将其逐步完善并开发一个真正可用的网站。
      1、main()方法启动了一个HTTP服务器程序，这个程序默认监听8080端口，并将HTTP请求转发给我们的应用来处理
      2、@Controller标注表示Application类是一个处理HTTP请求的控制器，该类中所有被@RequestMapping标注的方法都会用来处理对应URL的请求，
      @ResponseBody标注告诉Spring MVC直接将字符串作为Web响应（Reponse Body）返回，如果@ResponseBody标注的方法返回一个对象，
      则会自动将该对象转换为JSON字符串返回
      3、index()方法上包含@GetMapping("/")标注，意味着在浏览器中访问http://localhost:8080/ 
      （不考虑协议、host和port信息后的路径为"/"）后浏览器发送的请求会交给该方法进行处理
      4、index()方法直接返回一个字符串，那么相当于直接将字符串"Hello World"作为HTTP请求的响应返回给浏览器，
      于是我们在浏览器中可以看到相应的返回值。如果我们现在已经有一个用HTML语言编写的网页想让互联网上的其他人也能够通过这样的方式访问，
      那么最简单的办法就是把网页的内容作为index()方法的返回值，如下
```java
@RequestMapping("/")
@ResponseBody
public String index() {
    return "<html><head><title>Hello World!</title></head><body><h1>Hello World!</h1><p>This is my first web site</p></body></html>";
}
```


1、放置应用的main类

    通常建议将应用的main类放到其他类所在包的顶层(root package)，并将@EnableAutoConfiguration注解到你的main类上，这样就隐式地定义了一个基础的包搜索路径(search package)，以搜索某些特定的注解实体(比如@Service，@Component等)。

    采用root	package方式，你就可以使用@ComponentScan注解而不需要指定basePackage属性，也可以使用@SpringBootApplication注解，只要将main类放到root package中。
```java
com	+-	example					
        +-	myproject
            +-	Application.java			
            |
            +-	domain
            |	+-	Customer.java
            |   +-	CustomerRepository.java
            |
            +-	service
            |	+-	CustomerService.java	
            |
            +-	web	
              +-	CustomerController.java
```

```java
package	com.example.myproject;
import	org.springframework.boot.SpringApplication;
import	org.springframework.boot.autoconfigure.EnableAutoConfiguration; 
import	org.springframework.context.annotation.ComponentScan; 
import	org.springframework.context.annotation.Configuration;

@Configuration
@EnableAutoConfiguration
@ComponentScan 
public	class	Application	{
				public	static	void	main(String[]	args)	{								
                SpringApplication.run(Application.class,	args);			
                }
}
```java

2、自动配置
   SpringBoot自动配置（auto-configuration）尝试根据添加的jar依赖自动配置你的Spring应用
   
   实现自动配置有两种可选方式，分别是 将	@EnableAutoConfiguration	或	@SpringBootApplication	注解到	@Configuration	类上。
  注：你应该只添加一个	@EnableAutoConfiguration	注解，通常建议将它添加到主配置类 （primary		@Configuration	）上。


3、禁用特定的自动配置项
   如果发现启用了不想要的自动配置项，你可以使用@EnableAutoConfiguration注解的exclude属性禁用他们：
```java
package com.trs.controller;

import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.context.annotation.Configuration;

/**
 * Created by wangjie on 2016/10/17 0017.
 */
@Configuration
@EnableAutoConfiguration(exclude = {App.class})
public class MyConfiguation {
}
```
如果该类不在classpath中，你可以使用该注解的excludeName属性，并指定全限定名来达到 相同效果。最后，你可以通过	spring.autoconfigure.exclude	属性exclude多个自动配置项（一 个自动配置项集合）。

4、Spring Beans和依赖注入
你可以自由地使用任何标准的Spring框架技术去定义beans和他们注入的依赖。简单起见，我们经常使用@ComponentScan注解搜索beans，并结合@Autowired构造器注入。

如果你遵循以上的建议组织代码结构(将应用的main类放到包的最上层，即root package)，那么你就可以添加@ComponentScan注解而不需要任何参数，所有应用组件（@Component，@Service，@Repository，@Controller等）都会自动注册成Spring Beans.
```java
package com.trs.project.service;


import com.trs.project.dao.ReviewDao;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

/**
 * Created by wangjie on 2016/10/17 0017.
 */
@Service
public class ReviewService {

    private ReviewDao reviewDao;

    /**
     * 通过一个构造器注入获取一个ReviewDao bean，可以通过任何一个方法来获取这样的bean
     * @param reviewDao
     */
    @Autowired
    public ReviewService(ReviewDao reviewDao){
        this.reviewDao=reviewDao;
    }
}
```

5、使用@SpringBootApplication注解
 很多Spring Boot开发者经常使 用@Configuration，@EnableAutoConfiguration，@ComponentScan注解他们的main类，
 由于这些注解如此频繁地一块使用（特别是遵循以上最佳实践的时候），Spring	Boot就提供了一 个方便的@SpringBootApplication注解作为代替。

##### App.java
```java
package com.trs.project;

import com.trs.project.service.ReviewService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * Created by wangjie on 2016/10/17 0017.
 */

@RestController
//@Configuration
//@EnableAutoConfiguration
//@ComponentScan
@SpringBootApplication  //代替上面三个注解的功能
public class App {

    @Autowired
    private ReviewService reviewService;

    @RequestMapping("/")
    public String hello(){
        return reviewService.getString();
    }

    public static void main(String[] args){
        SpringApplication.run(App.class);
    }
}
```

##### ReviewService.java
```java
package com.trs.project.service;


import com.trs.project.dao.ReviewDao;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

/**
 * Created by wangjie on 2016/10/17 0017.
 */
@Service
public class ReviewService {

    private ReviewDao reviewDao;

    /**
     * 通过一个构造器注入获取一个ReviewDao bean，可以通过任何一个方法来获取这样的bean
     * @param reviewDao
     */
    @Autowired
    public ReviewService(ReviewDao reviewDao){
        this.reviewDao=reviewDao;
    }

    public String getString(){
        return reviewDao.getString()+"\n Hello here is ReviewService";
    }
}
```

##### ReviewDao.java
```java
package com.trs.project.dao;

import org.springframework.stereotype.Repository;

/**
 * Created by wangjie on 2016/10/17 0017.
 */
@Repository
public class ReviewDao {

    public String getString(){
        return "Hello Here is ReviewDao";
    }

}
```
























地址：https://course.tianmaying.com/spring-mvc+router#0
