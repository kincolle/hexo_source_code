---
title: Bean Post Processor
date: 2018-08-04 19:51:13
tags:
categories:
- Spring
- SpringFramework
- BasicKnowledge
- Bean Post Processor
---
## Interface Introduction
There is an interface in spring named BeanPostProcessor. It has 2 functions:

	public interface BeanPostProcessor
	{
	
	    public abstract Object postProcessBeforeInitialization(Object obj, String s)
	        throws BeansException;
	
	    public abstract Object postProcessAfterInitialization(Object obj, String s)
	        throws BeansException;
	}

We can know it from the name of its function:

1. postProcessBeforeInitialization: Operate before init
2. postProcessAfterInitialization: Operate after init

## Source Code
We find its source from the AbstractAutowireCapableBeanFactory class:

	protected Object initializeBean(final String beanName, final Object bean, RootBeanDefinition mbd) {
        if (System.getSecurityManager() != null) {
            AccessController.doPrivileged(new PrivilegedAction<Object>() {
                @Override
                public Object run() {
                    invokeAwareMethods(beanName, bean);
                    return null;
                }
            }, getAccessControlContext());
        }
        else {
            invokeAwareMethods(beanName, bean);
        }

        Object wrappedBean = bean;
        if (mbd == null || !mbd.isSynthetic()) {
            wrappedBean = applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName);
        }

        try {
            invokeInitMethods(beanName, wrappedBean, mbd);
        }
        catch (Throwable ex) {
            throw new BeanCreationException(
                    (mbd != null ? mbd.getResourceDescription() : null),
                    beanName, "Invocation of init method failed", ex);
        }

        if (mbd == null || !mbd.isSynthetic()) {
            wrappedBean = applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName);
        }
        return wrappedBean;
    }

And the source code of applyBeanPostProcessorsBeforeInitialization and applyBeanPostProcessorsAfterInitialization is:　　

	public Object applyBeanPostProcessorsBeforeInitialization(Object existingBean, String beanName)
            throws BeansException {

        Object result = existingBean;
        for (BeanPostProcessor beanProcessor : getBeanPostProcessors()) {
            result = beanProcessor.postProcessBeforeInitialization(result, beanName);
            if (result == null) {
                return result;
            }
        }
        return result;
    }

	public Object applyBeanPostProcessorsAfterInitialization(Object existingBean, String beanName)
            throws BeansException {

        Object result = existingBean;
        for (BeanPostProcessor beanProcessor : getBeanPostProcessors()) {
            result = beanProcessor.postProcessAfterInitialization(result, beanName);
            if (result == null) {
                return result;
            }
        }
        return result;
    }

From the code we know around invokeInitMethods, spring will invoke BeanPostProcessor and do its functions. In invokeInitMethods, there are 2 important points:

1. Judge whether extends InitializingBean, if so then invoke afterPropertiesSet().
2. Judge whether set the init-method, if so then do the function which is setted.


	protected void invokeInitMethods(String beanName, final Object bean, RootBeanDefinition mbd)
            throws Throwable {

        boolean isInitializingBean = (bean instanceof InitializingBean);
        if (isInitializingBean && (mbd == null || !mbd.isExternallyManagedInitMethod("afterPropertiesSet"))) {
            if (logger.isDebugEnabled()) {
                logger.debug("Invoking afterPropertiesSet() on bean with name '" + beanName + "'");
            }
            if (System.getSecurityManager() != null) {
                try {
                    AccessController.doPrivileged(new PrivilegedExceptionAction<Object>() {
                        @Override
                        public Object run() throws Exception {
                            ((InitializingBean) bean).afterPropertiesSet();
                            return null;
                        }
                    }, getAccessControlContext());
                }
                catch (PrivilegedActionException pae) {
                    throw pae.getException();
                }
            }
            else {
                ((InitializingBean) bean).afterPropertiesSet();
            }
        }

        if (mbd != null) {
            String initMethodName = mbd.getInitMethodName();
            if (initMethodName != null && !(isInitializingBean && "afterPropertiesSet".equals(initMethodName)) &&
                    !mbd.isExternallyManagedInitMethod(initMethodName)) {
                invokeCustomInitMethod(beanName, bean, mbd);
            }
        }
    }

## Example
#### 1. Define interface and implementation

DemoService interface

	public interface DemoService {
	    public void sayHello();
	}

DemoServiceImpl 

	public class DemoServiceImpl implements DemoService,NameInit {
	    String name;
	
	    @Override
	    public void sayHello() {
	        System.out.println("hello "+name);
	    }
	
	    @Override
	    public void setName(String name) {
	        this.name=name;
	    }
	}

#### 2. Define bean

        <bean id="demoService" class="com.zjl.beanpostprocessor.DemoServiceImpl">
        </bean>

#### 3. Define inject interface

	public interface NameInit {
	    public void setName(String name);
	}

#### 4. Define a BeanPostProcessor instence and if it extends NameInit then inject name.


	public class NameBeanPostProcessor implements BeanPostProcessor {
	    String name;
	
	    public String getName() {
	        return name;
	    }
	
	    public void setName(String name) {
	        this.name = name;
	    }
	
	    @Override
	    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
	        if(bean instanceof NameInit){
	            ((NameInit)bean).setName(name);
	        }
	        return bean;
	    }
	
	    @Override
	    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
	        return bean;
	    }
	}

#### 5. Define bean and inject name value

        <bean id="nameBeanPostProcessor" class="com.zjl.beanpostprocessor.NameBeanPostProcessor">
            <property name="name" value="kincolle"></property>
        </bean>

#### 6. define another BeanPostProcessor and print log

	public class LogBeanPostProcessor implements BeanPostProcessor {
	
	    @Override
	    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
	        System.out.println("running:"+beanName);
	        return bean;
	    }
	
	    @Override
	    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
	        System.out.println("running done:"+beanName);
	        return bean;
	    }
	}

#### 7. Define bean

        <bean id="logBeanPostProcessor" class="com.zjl.beanpostprocessor.LogBeanPostProcessor">
        </bean>

#### 8. Test

	public class BeanPostProcessorTest {
	    public static void main(String[] args) {
	        ApplicationContext context=new FileSystemXmlApplicationContext("beanpostprocessor.xml");
	        DemoService demoService=(DemoService) context.getBean("demoService");
	        demoService.sayHello();
	    }
	}

#### 9. Result

	running:demoService
	running done:demoService
	hello kincolle