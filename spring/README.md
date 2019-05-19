 65wert
spring 源码分析

### [BeanDefinition](BeanDefinition.md)

### [DefaultResourceLoader](DefaultResourceLoader.md)

### [XmlBeanDefinitionReader](XmlBeanDefinitionReader.md)

### [BeanDefinitionRegistry](BeanDefinitionRegistry.md)

### [BeanFactoryPostProcessor](BeanFactoryPostProcessor.md)

### [BeanPostProcessor](BeanPostProcessor.md)

###  AbstractBeanFactory 

###  BeanFactory

### DefaultListableBeanFactory

### AutowireCapableBeanFactory

### AbstractAutowireCapableBeanFactory

### SingletonBeanRegistry

### DefaultSingletonBeanRegistry

大牛博客参考
http://www.tianxiaobo.com/

bean 加载过程

https://blog.csdn.net/fd2025/article/details/80103791#





1 扫描加载资源文件， 通过BeanFactoryPostProcessor 处理器加载类的元数据， 解析分装成beabDefinition ，然后通过BeanFacotryRegisty 注册到  DefaultListableBeanFactory

2 注册后置处理器BeanPostProcessor



3 实力化  @Bean, @Resource,@Autowire,getBean 时， 先会从内存中获取（DefaultSingletonBeanFacotryRegistry）获取实力， 

4 若是内存不存在则，通过获取 Beandefinition 类的定义属性，开始对bean 进行实例化



5  检查是否有父类， 有则优先实例化父类，

6  查找是否存在依赖，实例化依赖，若是存储在依赖冲突，则会通过 DefaultSingletonBeanFacotryRegistry 中earlySingletonObjects 赖进行实例化Bean

7 执行实例化成功子之后， 执行后置处理器 BeanPostProcessor 中 postProcessorBeforeInstanitation方法

8 执行初始化方法 init-method ， @PostConstruct

9  后置处理器执行，后置处理器 BeanPostProcessor 中 postProcessorBeforeInstanitation方法

10 销毁后置处理器



![](D:\学习笔记\spring\images\获取bean的过程.png)

 





设计模式：

1 单例模式

* DefaultSingletonBeanFacoryRegister

2 工厂模式

* 

3 观察这模式

* **ApplicationListener**
* **ApplicationEventPublisher**

4 装饰器模式

* 

5 代理模式

* cglib ，java 动态代理
* mybatis 中 dao 接口的动态代理

6 模板方法模式

* JdbcTemplate

7 策略模式

8 建设者模式  BeanDefinitionBuilder

10  **抽象工厂**

* 工厂的例子是org.springframework.beans.factory.BeanFactory。通过它的实现，我们可以从Spring的容器访问bean。根据采用的策略，getBean方法可以返回已创建的对象（共享实例，单例作用域）或初始化新的对象（原型作用域）。在BeanFactory的实现中，我们可以区分：ClassPathXmlApplicationContext，XmlWebApplicationContext，StaticWebApplicationContext，StaticPortletApplicationContext，GenericApplicationContext，StaticApplicationContext。

11 适配器模式