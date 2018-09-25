---
title: RequestParam
date: 2018-08-19 11:01:29
tags:
categories:
- Spring
- SpringMVC
- RequestParam

---
## Introduction of @RequestParam
The @RequestParam has 3 attributes:
  
- value：name of param of the request
- required: whether it is necessary. The defualt value is true which means it is necessary.
- defaultValue: the defualt value. If you set it then the required will be set as false.

Here is how to set value
- one value: @RequestParam("")  or  @RequestParam(value="")
- more than one values: @RequestParam(value="", required=true, defaultValue="")

##### example

	@RequestMapping(value="/user/show")
	public ModelAndView show(@RequestParam(value="userId",defaultValue="1")
	 Long userId) {
		ModelAndView mv =
		new ModelAndView("show");
		mv.addObject("msg",
		"User ID：" + userId);
		return mv;
	}
	
