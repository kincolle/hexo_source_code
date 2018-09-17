---
title: Factory Bean And Bean Factory
date: 2018-08-03 22:51:13
tags:
categories:
- Spring
- SpringFramework
- Factory Bean And Bean Factory
---
## 1. BeanFactory
The BeanFactory which is end with Factory, is a factory interface that is used for managing beans. In spring, BeanFactory is a core interface in IoC container and its duty is intancing, pointing and setting denpendencies. 

The Spring have many implementation of BeanFactory and the XmlBeanFactory is one of them which is using XML to describe relationship of beans.

Intancing the container

	Resource resource = new FileSystemResource("beans.xml");
	BeanFactory factory = new XmlBeanFactory(resource);

or

	ClassPathResource resource = new ClassPathResource("beans.xml");
	BeanFactory factory = new XmlBeanFactory(resource);

or

	ApplicationContext context = new ClassPathXmlApplicationContext(new String[] {"applicationContext.xml", "applicationContext-part2.xml"});
	BeanFactory factory = (BeanFactory) context;

Then we can use getBean(String beanName) function to get the bean intance and there are 6 functions can be used:

- boolean containsBean(String beanName) : It is used for judging whether the container has the bean. If so return ture.
- Object getBean(String) : It gets a bean with the BeanName. If the scope of bean is singleton then return a sheared bean, if it is prototype then return a new bean, if there is no bean with the BeanName then return exception.
- Object getBean(String, Class) : Return a bean with a BeanName and change its type to the class
- Class getType(String name) : Return a class type of a bean with the BeanName.
- boolean isSingleton(String) : Judging whether the scope of the bean is singleton
- String[] getAliases(String name) : Return all Aliases of a bean with the BeanName.

## 2. FactoryBean
The FactoryBean which is end with Bean is a bean but it is special. It implements FactoryBean<T> interface and you can get a bean from getObject() of FactoryBean which is not FactoryBean it self by using ID. If you want to get the FactoryBean, plus a & in front of ID.

Here is an exmple of inmplements a FactoryBean and proxy a instence and handler all function with a LOG.

	
	public class MyFactoryBean implements FactoryBean<Object>, InitializingBean, DisposableBean {
	
	    private static final Logger logger = LoggerFactory.getLogger(MyFactoryBean.class);
	    
	    private String interfaceName;
	    
	    private Object target;
	    
	    private Object proxyObj;
	    
	    @Override
	    public void destroy() throws Exception {
	        logger.debug("destroy......");
	    }
	
	    @Override
	    public void afterPropertiesSet() throws Exception {
	        proxyObj = Proxy.newProxyInstance(
	                this.getClass().getClassLoader(), 
	                new Class[] { Class.forName(interfaceName) }, 
	                new InvocationHandler() {
	                    
	            @Override
	            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
	                logger.debug("invoke method......" + method.getName());
	                logger.debug("invoke method before......" + System.currentTimeMillis());
	                Object result = method.invoke(target, args);
	                logger.debug("invoke method after......" + System.currentTimeMillis());
	                return result;
	            }
	            
	        });
	        logger.debug("afterPropertiesSet......");
	    }
	
	    @Override
	    public Object getObject() throws Exception {
	        logger.debug("getObject......");
	        return proxyObj;
	    }
	
	    @Override
	    public Class<?> getObjectType() {
	        return proxyObj == null ? Object.class : proxyObj.getClass();
	    }
	
	    @Override
	    public boolean isSingleton() {
	        return true;
	    }
	
	    public String getInterfaceName() {
	        return interfaceName;
	    }
	
	    public void setInterfaceName(String interfaceName) {
	        this.interfaceName = interfaceName;
	    }
	
	    public Object getTarget() {
	        return target;
	    }
	
	    public void setTarget(Object target) {
	        this.target = target;
	    }
	
	    public Object getProxyObj() {
	        return proxyObj;
	    }
	
	    public void setProxyObj(Object proxyObj) {
	        this.proxyObj = proxyObj;
	    }
	
	}
	
XML-Bean config is

	<bean id="fbHelloWorldService" class="com.kincolle.MyFactoryBean">
	    <property name="interfaceName" value="com.kincolle.HelloWorldService" />
	    <property name="target" ref="helloWorldService" />
	</bean>

Junit Test class

	@RunWith(JUnit4ClassRunner.class)
	@ContextConfiguration(classes = { MyFactoryBeanConfig.class })
	public class MyFactoryBeanTest {
	    @Autowired
	    private ApplicationContext context;
	    
	    @Test
	    public void testFactoryBean() {
	        HelloWorldService helloWorldService = (HelloWorldService) context.getBean("fbHelloWorldService");
	        helloWorldService.getBeanName();
	        helloWorldService.sayHello();
	    }
	}

