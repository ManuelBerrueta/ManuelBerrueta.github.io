---
layout: post
title: Scripting in Bruno
categories: Proxy Tools
---

# Scripting in Bruno
### The backstory
During engagements I use code for automation, to manipulate or modify request/payloads, as well as creating dynamic values. This time around I wanted to leverage the scripting capabilities of Bruno, instead of using a stand alone script. I am going to break down how you can use scripts (dynamic code) within your request Lets get to it...

### POC or GTFO...
In this exercise I am going to show how to use a script to create a dynamic value for a GUID in a variable used in the body of the request. It sounds a bit more complicated then it actually is, stick with me ;). 

1. Create a variable in the request by going to **Vars** -> "+ Add" -> Name it "**`myGUID`**" and you can leave a blank value.
2. Add the **`myGUID`** variable within the **Body** of the request by encapsulating the variable name in double curly brackets, like so: **`{{myGUID}}`**. Here is an example of the JSON Body of a request using it:
   ```json
	{
		"Random Guid": "{{ '{{' }}myGUID}}",
	}
	```
   
3. In the Script section, in the **Pre Request** section add the following code:
	```javascript
	function generateGUID() {
		function s4() {
			return Math.floor((1 + Math.random()) * 0x10000)
				.toString(16)
				.substring(1);
		}
		return s4() + s4() + '-' + s4() + '-' + s4() + '-' + s4() + '-' + s4() + s4() + s4();
	}
	
	const newGUID = generateGUID();
	bru.setVar("myGUID",newGUID)
	```

To summarize, when you execute the request:
1. The Pre Request script executes
2. It creates a new GUID and sets that new GUID in the myGUID variable using **`bru.setVar("myGUID",newGUID)`**
3. Since the myGUID is used within the Body of the request, it is replaced with the new value of myGUID
4. The request is sent with that dynamically generated value

> [!TIP] You can also use the variables in other places not limited to the Headers and the endpoint URL

