---
title: RequestBody And ResponseBody
date: 2018-08-19 14:07:51
tags:
categories:
- Spring
- SpringMVC
- RequestBody And ResponseBody

---
## Introduction of  @ResponseBody
When @the ResponseBody is set on method the the result will be written into HTTP response body. And, @RequestBody will let HTTP request use write HttpMessageConverter to  get your entity.  

	@RequestMapping(value = "/test")
	public class myController {
	    @RequestMapping(value = "/aaa")
	    public String getMyName() {
	        return "test";
	    }
	}



## Introduction of  @RequestBody
@RequestBody is used on param and change xml or json data into JavaBean instance, which will use HttpMessageConverter to do this function.

	@RequestMapping("/login.do")
    @ResponseBody
    public Object login(@RequestBody User loginUuser, HttpSession session) {
        user = userService.checkLogin(loginUser);
        session.setAttribute("user", user);
        return new JsonResult(user);
    }