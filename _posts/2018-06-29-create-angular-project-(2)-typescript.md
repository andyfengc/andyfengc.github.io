---
layout: post
title: Create Angular v2+ project (2) - typescript
author: Andy Feng
---

# Introduction #
check version

# New features
## Pick vs Partial
`Partial` and `Pick` are mapped types. They are used to create a new type using part of original type.

e.g.

	interface PartialTask {
	  id: string,
	  name: string,
	  contacts: any[]
	}
	
	interface Task extends PartialTask{
	  associatedJob: string,
	  submissionDate: string,
	  allocatedTime: number,
	  expectedCompletion: string,
	  assignee: string,
	  invoiceNumber: string,
	  invoiceDueDate: string,
	  comment: string,
	  taskAddress: string
	  ...
	  ... x 10
	}

Here we have 2 versions of `Task` type.

`Partial` helps us create a clone type that mirrors a provided type(interface, class) with all properties:

	type PartialTaskByPartial = Partial<Task>;
	let taskObj : PartialTaskByPartial = null;

Here the new type `PartialTaskByPartial` contains all properties of Task class. 

`Pick` is alternative to the above but gives us more powerful way. We can pick some of properties from original type to get a new type:

	type PartialTaskByPick = Pick<Task, 'id' | 'associatedJob'>;
	let partialTaskObj : PartialTaskByPick = null;

Here the new type `PartialTaskByPick` only contains 2 properties: id, associatedJob

### Demo 1
In practice, `Pick` can help us create new objects with customized proproties from original types. e.g.
	
	function pick<T, K extends keyof T>(obj: T, ...keys: K[]): Pick<T, K> {
	    const copy = {} as Pick<T, K>;
	
	    keys.forEach(key => copy[key] = obj[key]);
	
	    return copy;
	}

Then:

	let originalObj = { "name": "someName", "age": 20, "date": "2015-01-03" };
	let copy = pick(originalObj, "name", "age") as Test;
	console.log(copy); // { name: "someName", "age": 20 }

Please note that here `Pick` help us achieve this purpose via new empty object {} and add properties and copy value from original object into it. It has better performance than delete a property from original object.

### Demo 2
`Pick` is handy when you need to create a new type from an existing interface with only the specified keys, which is great but sometimes you need just the opposite. Like when you're defining the return type for your API endpoint where you want to include everything except for one or two private fields.

So what you actually need is `Omit`. It is not built-in and we will create for that:

    type Diff<T extends string, U extends string> = ({[P in T]: P } & {[P in U]: never } & { [x: string]: never })[T];  
    type Omit<T, K extends keyof T> = Pick<T, Diff<keyof T, K>>; 

this is how to use:

	type PartialTaskByOmit = Omit<Employee, 'id' | 'associatedJob'>;  
	var partialTaskObj : PartialTaskByOmit ;

Here the new type `PartialTaskByOmit` contains all properties of Task class except id, associatedJob

# References
[https://medium.com/@curtistatewilkinson/typescript-2-1-and-the-power-of-pick-ff433f1e6fb](https://medium.com/@curtistatewilkinson/typescript-2-1-and-the-power-of-pick-ff433f1e6fb)

[http://ideasintosoftware.com/typescript-advanced-tricks/](http://ideasintosoftware.com/typescript-advanced-tricks/)