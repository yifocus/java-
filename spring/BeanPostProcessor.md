



# BeanPostProcessor



**BeanPostProcessor**：bean级别的处理，针对某个具体的bean进行处理

接口提供了两个方法，分别是初始化前和初始化后执行方法，具体这个初始化方法指的是什么方法，类似我们在定义bean时，定义了init-method所指定的方法<bean id = "xxx" class = "xxx" init-method = "init()">

这两个方法分别在init方法前后执行，需要注意一点，我们定义一个类实现了BeanPostProcessor，默认是会对整个Spring容器中所有的bean进行处理。

既然是默认全部处理，那么我们怎么确认我们需要处理的某个具体的bean呢？

可以看到方法中有两个参数。类型分别为Object和String，第一个参数是每个bean的实例，第二个参数是每个bean的name或者id属性的值。所以我们可以第二个参数，来确认我们将要处理的具体的bean。

这个的处理是发生在Spring容器的实例化和依赖注入之后。



```

public interface BeanPostProcessor {  
  
    /** 
     * Apply this BeanPostProcessor to the given new bean instance <i>before</i> any bean 
     * initialization callbacks (like InitializingBean's {@code afterPropertiesSet} 
     * or a custom init-method). The bean will already be populated with property values.    
     */  
    //实例化、依赖注入完毕，在调用显示的初始化之前完成一些定制的初始化任务  
    Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException;  
  
      
    /** 
     * Apply this BeanPostProcessor to the given new bean instance <i>after</i> any bean 
     * initialization callbacks (like InitializingBean's {@code afterPropertiesSet}   
     * or a custom init-method). The bean will already be populated with property values.       
     */  
    //实例化、依赖注入、初始化完毕时执行  
    Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException;  
  
}
```