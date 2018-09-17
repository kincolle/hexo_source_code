---
title: Validator
date: 2018-08-06 22:11:49
tags:
categories:
- Spring
- SpringFramework
- BasicKnowledge
- Validator
---

In org.springframework.validation package, there has some function for validating data and Validator interface is one of them. Here let's see an example:

Person domain:

	public class Person {
	    private String name;
	    private Integer age;
	
	    public String getName() {
	        return name;
	    }
	
	    public void setName(String name) {
	        this.name = name;
	    }
	
	    public Integer getAge() {
	        return age;
	    }
	
	    public void setAge(Integer age) {
	        this.age = age;
	    }
	}

Define PersonValidator which implements Validator

	public class PersonValidator implements Validator {
	    @Override
	    public boolean supports(Class<?> clazz) {
	        return Person.class.equals(clazz);
	    }
	    @Override
	    public void validate(Object obj, Errors e) {
	        ValidationUtils.rejectIfEmpty(e, "name", "name.empty");
	        Person p = (Person) obj;
	        if (p.getAge() < 0) {
	            e.rejectValue("age", "negativevalue");
	        } else if (p.getAge() > 110) {
	            e.rejectValue("age", "kincolle");
	        }
	    }
	}

controller

	@RestController
	public class FooController {
	    @RequestMapping("/peopleTest")
	    public String people(Person person){
	
	        PersonValidator validator = new PersonValidator();
	        if(validator.supports(Person.class)){
	            BindException errors = new BindException(person, "person");
	            validator.validate(person,errors);
	            List<ObjectError> allErrors = errors.getAllErrors();
	            System.out.println("size="+allErrors.size());
	            for (int i=0;i<allErrors.size();i++) {
	                System.out.println(allErrors.get(i).getCode());
	            }
	
	        }
	        return "success";
	    }
	}

Star Spring boot 

	@SpringBootApplication(exclude={DataSourceAutoConfiguration.class})
	public class Application {
	    public static void main(String[] args) {
	        SpringApplication.run(Application.class,args);
	    }
	}

We input  http://localhost:8080/peopleTest?age=200

The output result is:

	size=2
	name.empty
	kincolle

If we do not input age like http://localhost:8080/peopleTest then NullPointException will happen. 
  