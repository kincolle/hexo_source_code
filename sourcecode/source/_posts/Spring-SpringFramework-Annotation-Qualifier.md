---
title: Qualifier
date: 2018-08-05 16:51:19
tags:
categories:
- Spring
- SpringFramework
- Annotation
- Qualifier
---
## Introduction
@Autowired is done according to type of bean. If more than one bean with the same tyep, then BeanCreationException will happen. To solve this problem, we use @Autowired and @Qualifier together.

## Example

Interface

	public interface EmployeeService {
	    public EmployeeDto getEmployeeById(Long id);
	}

We have 2 implementations:

	@Service("service")
	public class EmployeeServiceImpl implements EmployeeService {
	    public EmployeeDto getEmployeeById(Long id) {
	        return new EmployeeDto();
	    }
	}
	
	@Service("service1")
	public class EmployeeServiceImpl1 implements EmployeeService {
	    public EmployeeDto getEmployeeById(Long id) {
	        return new EmployeeDto();
	    }
	}

Then invoke it:

	@Controller
	@RequestMapping("/emplayee.do")
	public class EmployeeInfoControl {
	    
	    @Autowired
	    EmployeeService employeeService;
	     
	    @RequestMapping(params = "method=showEmplayeeInfo")
	    public void showEmplayeeInfo(HttpServletRequest request, HttpServletResponse response, EmployeeDto dto) {
	        ...
	    }
	}

When we star the tomcat, the BeanCreationException will happen:
	
	org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'employeeInfoControl': Injection of autowired dependencies failed; nested exception is org.springframework.beans.factory.BeanCreationException: Could not autowire field: com.kincolle.service.EmployeeService com.kincolle.controller.EmployeeInfoControl.employeeService; nested exception is org.springframework.beans.factory.NoSuchBeanDefinitionException: No unique bean of type [com.kincolle.service.EmployeeService] is defined: expected single matching bean but found 2: [service1, service2]

From the exception we know we have 2 implementions, @Autowired won't know which one should be chosen. To solve this problem we can do like the following:

	@Controller
	@RequestMapping("/emplayee.do")
	public class EmployeeInfoControl {
	    
	    @Autowired
	    @Qualifier("service")
	    EmployeeService employeeService;
	    
	    @RequestMapping(params = "method=showEmplayeeInfo")
	    public void showEmplayeeInfo(HttpServletRequest request, HttpServletResponse response, EmployeeDto dto) {
	        ...
	    }
	}