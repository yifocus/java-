# Spring 源码分析                   

## spring 初始化bean 

### BeanDefinition

* **如 xml 中的bean 或者配置文件中的@Bean**

* 装载类的基本信息如：**类名**、**`scope`**、**属性**、**构造函数参数列表**、**依赖的`bean`**、**是否是单例类**、**是否是懒加载**等，其实就是**将`Bean`的定义信息存储到这个`BeanDefinition`相应的属性中，后面对Bean的操作就直接对`BeanDefinition`进行**，例如拿到这个**`BeanDefinition`**后，可以**根据里面的类名、构造函数、构造函数参数，使用反射进行对象创建**
* 可以通过 **`ConfigurableListableBeanFactory#getBeanDefinition`** 即其实现类 **`DefaultListableBeanFactory`** 获取实例

![](images\beanDefinitionInterface.png)





#### **AttributeAccessor**

类似于map，具有保存和访问name/value属性的能力

```java
public interface AttributeAccessor {
    /**
	 * Set the attribute defined by {@code name} to the supplied {@code value}.
	 * If {@code value} is {@code null}, the attribute is {@link #removeAttribute removed}.
	 * <p>In general, users should take care to prevent overlaps with other
	 * metadata attributes by using fully-qualified names, perhaps using
	 * class or package names as prefix.
	 * @param name the unique attribute key
	 * @param value the attribute value to be attached
	 */
	void setAttribute(String name, @Nullable Object value);
	
	@Nullable
	Object getAttribute(String name);
	
	@Nullable
	Object removeAttribute(String name);
	
	boolean hasAttribute(String name);
	
	String[] attributeNames();

}
```





#### **BeanMetadataElement**

source（配置源）的能力,持有Bean元数据元素，作用是可以持有XML文件的一个bean标签对应的Object

```java
public interface BeanMetadataElement {

	/**
	 * Return the configuration source {@code Object} for this metadata element
	 * (may be {@code null}).
	 */
	@Nullable
	Object getSource();

}
```



#### BeanDefinition

如 BeanDefinition 描述

```java


/**
 * A BeanDefinition describes a bean instance, which has property values,
 * constructor argument values, and further information supplied by
 * concrete implementations.
 *
 * <p>This is just a minimal interface: The main intention is to allow a
 * {@link BeanFactoryPostProcessor} such as {@link PropertyPlaceholderConfigurer}
 * to introspect and modify property values and other bean metadata.
 *
 * @see ConfigurableListableBeanFactory#getBeanDefinition
 * @see org.springframework.beans.factory.support.RootBeanDefinition
 * @see org.springframework.beans.factory.support.ChildBeanDefinition
 */
public interface BeanDefinition extends AttributeAccessor, BeanMetadataElement {

	// 单例模式
	String SCOPE_SINGLETON = ConfigurableBeanFactory.SCOPE_SINGLETON;
    // 多例模式
	String SCOPE_PROTOTYPE = ConfigurableBeanFactory.SCOPE_PROTOTYPE;


	// bean 的角色
    // 用户自定义bean
	int ROLE_APPLICATION = 0;
	// 组建支持
	int ROLE_SUPPORT = 1;
    //  后台角色，内部是用的bean 
	int ROLE_INFRASTRUCTURE = 2;

    // 设置/返回 父类的 BeanDefinition
	void setParentName(@Nullable String parentName);
	String getParentName();
    
	void setBeanClassName(@Nullable String beanClassName);

	@Nullable
	String getBeanClassName();

	void setScope(@Nullable String scope);
    
	@Nullable
	String getScope();
    
    // 懒加载
	void setLazyInit(boolean lazyInit);

	boolean isLazyInit();

	void setDependsOn(@Nullable String... dependsOn);

	String[] getDependsOn();

	void setAutowireCandidate(boolean autowireCandidate);

	boolean isAutowireCandidate();

	void setPrimary(boolean primary);

	boolean isPrimary();


	void setFactoryBeanName(@Nullable String factoryBeanName);

	String getFactoryBeanName();

	
	void setFactoryMethodName(@Nullable String factoryMethodName);

	@Nullable
	String getFactoryMethodName();


	ConstructorArgumentValues getConstructorArgumentValues();


	default boolean hasConstructorArgumentValues() {
		return !getConstructorArgumentValues().isEmpty();
	}

	MutablePropertyValues getPropertyValues();

	default boolean hasPropertyValues() {
		return !getPropertyValues().isEmpty();
	}

	void setInitMethodName(@Nullable String initMethodName);

	String getInitMethodName();

	void setDestroyMethodName(@Nullable String destroyMethodName);

	String getDestroyMethodName();


	void setRole(int role);

	int getRole();

	void setDescription(@Nullable String description);


	String getDescription();

	boolean isSingleton();


	boolean isPrototype();

	boolean isAbstract();

	String getResourceDescription();

	/**
	 * Return the originating BeanDefinition, or {@code null} if none.
	 * Allows for retrieving the decorated bean definition, if any.
	 * <p>Note that this method returns the immediate originator. Iterate through the
	 * originator chain to find the original BeanDefinition as defined by the user.
	 */
	@Nullable
	BeanDefinition getOriginatingBeanDefinition();

}

```



![](images\beanDefinition.png)



#### AnnotatedBeanDefinition 

   这个接口可以获取**BeanDefinition** 注解相关数据

```java
/**
 * Extended {@link org.springframework.beans.factory.config.BeanDefinition}
 * interface that exposes {@link org.springframework.core.type.AnnotationMetadata}
 * about its bean class - without requiring the class to be loaded yet.
 *
 * @author Juergen Hoeller
 * @since 2.5
 * @see AnnotatedGenericBeanDefinition
 * @see org.springframework.core.type.AnnotationMetadata
 */
public interface AnnotatedBeanDefinition extends BeanDefinition {

	/**
	 * Obtain the annotation metadata (as well as basic class metadata)
	 * for this bean definition's bean class.
	 * @return the annotation metadata object (never {@code null})
	 */
	AnnotationMetadata getMetadata();

	/**
	 * Obtain metadata for this bean definition's factory method, if any.
	 * @return the factory method metadata, or {@code null} if none
	 * @since 4.1.1
	 */
	@Nullable
	MethodMetadata getFactoryMethodMetadata();

}
```



#### AbstractBeanDefinition

**大部分类的通用属性实现**

**实现了tostring hashCode,equals 方法**



![](images\abstractBeanDefinition.png)





#### GenericBeanDefinition



XML中所有的配置都可以在GenericBeanDefinition的实例类中找到相应的配置。
GenericBeanDefinition只是子类实现，而大部分通用属性都保存在AbstractBeanDefinition中。

在GenericBeanDefinition基本上就只多了parentName属性。



![](images\GenericBeanDefinition.png)



```java
public class GenericBeanDefinition extends AbstractBeanDefinition {

	@Nullable
	private String parentName;
	public GenericBeanDefinition() {
		super();
	}
	public GenericBeanDefinition(BeanDefinition original) {
		super(original);
	}
	@Override
	public void setParentName(@Nullable String parentName) {
		this.parentName = parentName;
	}
	@Override
	@Nullable
	public String getParentName() {
		return this.parentName;
	}
	@Override
	public AbstractBeanDefinition cloneBeanDefinition() {
		return new GenericBeanDefinition(this);
	}
	@Override
	public boolean equals(Object other) {
		return (this == other || (other instanceof GenericBeanDefinition && super.equals(other)));
	}

	@Override
	public String toString() {
		StringBuilder sb = new StringBuilder("Generic bean");
		if (this.parentName != null) {
			sb.append(" with parent '").append(this.parentName).append("'");
		}
		sb.append(": ").append(super.toString());
		return sb.toString();
	}

}

```



#### RootBeanDefinition



```
从spring2.5开始，spring一开始都是使用GenericBeanDefinition类保存Bean的相关信息，在需要时，在将其转换为其他的BeanDefinition类型

是运行时使用的Bean视图，即spring会使用RootBeanDefinition初始化Bean
在源码注释中，有这么一句：It might have been created from multiple original bean definitions that inherit from each other，spring会依据多个具有继承关系的BeanDefinition（实际上是GenericBeanDefinition类型）来创建RootBeanDefinition，换句话说，在多继承体系中，RootBeanDefinition代表的是当前初始化类的父类的BeanDefinition

RootBeanDefinition可用于没有继承关系的Bean的创建
```



![](images\rootBeanDefinition.png)







#### ChildBeanDefinition

ChildBeanDefinition是一种bean definition，它可以继承它父类的设置，即ChildBeanDefinition对RootBeanDefinition有一定的依赖关系。

　　ChildBeanDefinition从父类继承构造参数值，属性值并可以重写父类的方法，同时也可以增加新的属性或者方法。(类同于java类的继承关系)。若指定初始化方法，销毁方法或者静态工厂方法，　　ChildBeanDefinition将重写相应父类的设置。depends on，autowire mode，dependency check，sigleton，lazy init 一般由子类自行设定。

> 注意：从spring 2.5 开始，提供了一个更好的注册bean definition类GenericBeanDefinition，它支持动态定义父依赖，方法是GenericBeanDefinition.setParentName(java.lang.String)，GenericBeanDefinition可以有效的替代ChildBeanDefinition的绝大分部使用场合。

![](images\ChildBeanDefinition.png)

#### AnnotatedGenericBeanDefinition

适用与已经被注册或者被扫描到的类



![](images\AnnotateGenericBeanDefinition.png)

#### ScannedGenericBeanDefinition

​    基于ASM ClassReader，与AnnotatedGenericBeanDefinition不同，用于能够被Spring容器扫描到的类(例如被@Component注解的类)



![](images\ScannedGenericBeanDefinition.png)





#### ConfigurationClassBeanDefinition

在@Configuration注解的类中，使用@Bean注解实例化的Bean，其定义会用ConfigurationClassBeanDefinition存储

```java
ConfigurationClassBeanDefinition是一个内部类，其外部类是ConfigurationClassBeanDefinitionReader，这个类负责将@Bean注解的方法转换为对应的ConfigurationClassBeanDefinition类

1、如果@Bean注解没有指定bean的名字，默认会用方法的名字命名bean
2、@Configuration注解的类会成为一个工厂类，而所有的@Bean注解的方法会成为工厂方法，通过工厂方法实例化Bean，而不是直接通过构造函数初始化
3、@Bean注解注释的类会使用构造函数自动装配

```

![](images\ConfigurationClassBeanDefinition.png)	