* **Spring模块划分**

![图片](https://uploader.shimo.im/f/s8KdzoiqwiZu8RDk.png!thumbnail)


---


* **IOC （inversion of control）控制反转**：
    * 何为控制：资源获取方式；
        * 主动式：需要什么资源自己创建什么资源；
            * BookServlet {

BookService bs = new BookService();//简单对象创建

AirPlane ap = new AirPlane();//对于复杂对象的创建是比较庞大的工程

}

        * 被动式：
            * BookeServlet{

@AutoWired

BookService bs ;

public void test1(){

bs.method();

}

}

    * 容器
        * 调试spring的源码,容器其实就是一个map,其中保存了所有创建好的bean,并提供外界获取功能
        * 管理所有的组件（有功能的类）；
            * BookServlet受容器管理，BookService也受容器管理。容器可以自动探查出那些组件（类）需要用到的另一些组件；容器帮我们创建BookService对象，并把BookService对象赋值过去；
    * 控制反转:主动的new资源变为被动的接收资源；
            * 相当于婚介所；将主动获取变成被动接受

---


* **DI （dependency injection）依赖注入：**
    * 容器能指导组件（类）运行的时候，需要另外的哪些类；并通过反射的形式，将容器中准备好的BookService对象注入（利用反射给属性赋值）到BookServlet中；
    * 只要容器管理的组件，都能使用容器提供的强大功能；

---


* **核心容器：**
    * beans
    * context
    * core
    * spel(expression)：spring expression language：spring表达式语言
```plain
spring-beans-4.0.0.RELEASE.jar
spring-context-4.0.0.RELEASE.jar
spring-core-4.0.0.RELEASE.jar
spring-expression-4.0.0.RELEASE.jar
```

---


* **一些细节：**
    * src ：源码包开始的路径，称为类路径的开始；
        * 所有源码包里面的东西都会被合并放在类路径里面：
            * java：  /bin/
            * web:    /WEB-INF/classes/
    * ApplicationContext（IOC容器的接口）：
        * ClassPathXmlApplicationContext("ioc.xml"）; 容器的配置文件在类路径下；
        * FileSystemXmlApplicationContext("F://ioc.xml");   磁盘路径；
    * 组件的创建工作由容器完成：
        * 什么时候创建好？
            * 容器中对象的创建在容器创建完成的时候就已经创建好了
        * 同一个组件在IOC容器中是单例的
        * 容器中如果没有这个组件，当获取这个组件时，报异常：
            * NoSuchBeanDefinitionException
        * IOC容器在创建这个组件对象的时候，会利用setter方法为javaBean属性进行赋值；
        * javaBean的属性名由getter/setter方法决定，而不是生命的变量名；


---


* **Bean的作用域（scope）：**
    * singleton（默认）:单实例
        * 在容器启动完成之前就已经创建好对象，保存在容器中了；
        * 任何获取都是获取之前创建好的那个对象；
    * prototype:多实例
        * 容器启动默认不会去创建多实例bean；
        * 获取的时候创建这个bean；
        * 每次获取都会创建一个新的对象；
    * request:在web环境下：同一次请求创建一个Bean实例（没用）；
    * session:在web环境下：同义词会话创建一个Bean实例（没用）；
* **Bean的生命周期：**
    * 单例：
        * （容器启动）构造器----->初始化方法----->（容器关闭）销毁方法
    * 多实例
        * 获取Bean（构造器----->初始化方法）----->容器关闭不会调用bean的销毁方法
* **Bean的后置处理器：**
    * 接口（BeanPostProcessor）
        * 实现类
            * postProcessBeforeInitialization
            * postProcessAfterIntialization
    * 单例：
        * （容器启动）构造器--后置before-->初始化方法--后置after-->(容器关闭)销毁方法
    * 无论bean是否有初始化方法，后置处理器都会默认其有，还会继续工作。

---


* **BeanFactory 创建工厂：**
    * 静态工厂：工厂本身不用创建对象，通过静态方法直接调用
    * 实例工厂：工厂本身需要创建对象


---


* **注解：**
    * 通过给bean添加某些注解，可以快速的将bean加入到ioc容器中
    * 某个类添加上任何一个注解都能快速的将这个组件加入到ioc容器的管理中；
        * Spring有四个注解：
            * @Controller：控制器
            * @Service：业务逻辑层
            * @Repository：持久化层/dao层
            * @Component：给不属于以上基层的组件添加这个注解：
        * 注解可以随便加，spring底层不会去验证是否你所注解的是否就是一个dao service；

只是推荐加，方便程序员看

    * 使用注解将组件快速的加入到容器中需要如下几步：
        * 标注注解；
        * 告诉spring，自动扫描添加了注解的组件：依赖context名称空间
        * 一定要导入aop包，支持加注解模式；
    * 组件的id和作用域配置：
        * id：
            * 默认为自检的类名首字母小写; 自定义:@Repository{"bookdaoxixi"}
        * 组件作用域:
            * 默认为单例; 自定义:  @Scope(value="prototype")
    * **@Autowired:**
        * @AutoWired

private BookService bookService;

        * 原理:
            * 先按照类型去容器中找到对应的组件:

bookService = ioc.getBean(Bookservice.class)

                    * 找到一个直接赋值
                    * 没找到:抛异常
                    * 找到多个?
                        * 只能找变量名作为id继续匹配:BookService(bookService),BookService(bookServiceExt)

@AutoWired

private BookService BookServiceExt2

                            * 匹配上:装配
                            * 没匹配上:报错
                                * 使用@Qualifier("bookService")指定一个id

找到匹配上,没找到报错

        * Autowired标注的自动装配属性默认是一定装配上的
            * 设置为:找到就匹配,找不到赋值null:@AutoWired(required=false);
    * @Autowired @Resource @Inject(EJB) 都是自动装配的意思
        * @Autowired:最强大; 是spring自己的注解
        * @Resource: j2EE;java的标准
        * 两者区别:
            * @Autowired脱离spring就不能使用了,所以@Resource扩展性更强


---


* **源码分析:**
    * IOC:
        * IOC是一个容器;
        * 容器启动的时候创建所有单实例对象;
        * 我们可以直接从容器中获取这个对象
    * springIOC:
        * ioc容器的启动过程?启动期间都做了什么?(什么时候创建所有单实例bean);
        * ioc是如何创建这些单实例bean,并如何管理的?到底保存在了那里?
* **思路:**

* **BeanFactory和ApplicationContext的区别:**
    * 口头回答:
        * BeanFactory是最底层的接口,ApplicationContext里给程序员使用的ioc容器接口;
        * ApplicationContext是BeanFactory的子接口;
    * ApplicationContext是BeanFactory的子接口;
        * BeanFactory:bean工厂接口;负责创建bean实例;容器里面保存的所有单例bean其实是一个map;spring最底层的接口;
        * ApplicationContext:是容器接口;更多的负责容器功能的实现;(可以基于beanfactory创建好的对象之上完成强大的容器),容器可以从map获取这个bean,在ApplicationContext接口下的这些类里面,实现AOP,DI;
    * Spring里面最大的模式就是工厂模式
        * Beanfactory:bean工厂;工厂模式;帮用户创建bean















