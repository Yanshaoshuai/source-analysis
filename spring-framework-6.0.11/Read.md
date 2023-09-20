#### BeanFactory

作用:用于访问Spring bean容器的根接口。Spring 的依赖注入功能是使用此 BeanFactory 接口及其子接口实现的。



#### BeanDefinitionRegistry

作用:注册和持有bean definition的接口，通常被内部使用 AbstractBeanDefinition 层次结构的 BeanFactory 实现。是 Spring 的 Bean 工厂包中唯一封装bean definition注册的接口。



#### BeanFactoryPostProcessor

作用:可以自定义修改application context 中的bean definition ,调整context底层bean工厂中bean中的属性。只能修改bean definition，不能修改bean实例。

ApplicationContext可以自动检测它bean容器中的BeanFactoryPostProcessor,也可以ConfigurableApplicationContext以通过编程的方式注册到容器中。自动检测的BeanFactoryPostProcessor应用的顺序通过@PriorityOrdered/@Ordered指定，顺序注解对编程方式注册的BeanFactoryPostProcessor不生效，编程方式注册的BeanFactoryPostProcessor按照注册顺序排序。

#### Aware

作用: 一个标记超接口，表示 Bean 有资格被Spring 容器的特定框架对象通过回调方法通知。实际的方法签名由各个子接口确定，但通常应仅包含一个接受单个参数的 void 返回方法



#### ClassPathXmlApplicationContext

作用: 一个简单、一站式的便利应用程序上下文。把传入的路径解释为classs path resource下的路径，包括包路径。

支持单个具体("/myfiles/context.xml")路径以及ant路径("/myfiles/*-context.xml")

**类结构** 

![image-20230917191707783](C:\Users\yan\AppData\Roaming\Typora\typora-user-images\image-20230917191707783.png)

只有一个configResources字段其余全是构造方法



**配置文件加载过程**

ClassPathXmlApplicationContext("beans.xml")

-->AbstractApplicationContext.refresh

-->AbstractApplicationContext.obtainFreshBeanFactory

-->AbstractRefreshableApplicationContext.refreshBeanFactory

--> AbstractXmlApplicationContext.loadBeanDefinitions

-->...

-->DefaultBeanDefinitionDocumentReader#processBeanDefinition



**直接父类**

AbstractXmlApplicationContext





#### AbstractXmlApplicationContext

作用:一个实现了ApplicationContext的便捷抽象类，通过XmlBeanDefinitionReader来从xml文件中构建bean。

子类只需实现getConfigResources/getConfigLocations来提供配置文件位置。重写getResourceByPath/getResourcePatternResolver可以用来拓展解析配置文件路径的方式。

**类结构**

![image-20230917195248612](C:\Users\yan\AppData\Roaming\Typora\typora-user-images\image-20230917195248612.png)

主要实现了loadBeanDefinitions方法

```java
	@Override
	protected void loadBeanDefinitions(DefaultListableBeanFactory beanFactory) throws BeansException, IOException {
		// Create a new XmlBeanDefinitionReader for the given BeanFactory.
		XmlBeanDefinitionReader beanDefinitionReader = new XmlBeanDefinitionReader(beanFactory);

		// Configure the bean definition reader with this context's
		// resource loading environment.
		beanDefinitionReader.setEnvironment(this.getEnvironment());
		beanDefinitionReader.setResourceLoader(this);
		beanDefinitionReader.setEntityResolver(new ResourceEntityResolver(this));

		// Allow a subclass to provide custom initialization of the reader,
		// then proceed with actually loading the bean definitions.
		initBeanDefinitionReader(beanDefinitionReader);
		loadBeanDefinitions(beanDefinitionReader);
	}
```

该方法的beanFactory将会被XmlBeanDefinitionReader用于注册bean definition。



#### XmlBeanDefinitionReader

作用:一个用于xml bean definition的bean definition reader。把真正读取xml 文档的工作委托给BeanDefinitionDocumentReader的一个实现。

这个类会将xml document加载出来然后交给BeanDefinitionDocumentReader读取出 bean definition。BeanDefinitionDocumentReader会把每一个bean definition注册到给定的bean factory，以便之后和BeanDefinitionRegistry进行沟通。







