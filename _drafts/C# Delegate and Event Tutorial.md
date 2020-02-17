---
layout: post
title: C# Delegate and Event Tutorial
author: Andy Feng
categories: [Delegate, Event, EventHandler, C#]
---

# Introduction #
A `delegate` is a type that represents references to methods with a particular parameter list and return type. When you instantiate a delegate, you can associate its instance with any method with a compatible signature and return type. You can invoke (or call) the method through the delegate instance.

Delegate type can be declared using the `delegate` keyword. Once a delegate is declared, delegate instance will refer and call those methods whose return type and parameter-list matches with the delegate declaration.

e.g.

	public delegate int Calcule(int x, int y);

Any method from any accessible class or struct that matches the delegate type can be assigned to the delegate. The method can be either static or an instance method. This makes it possible to programmatically change method calls, and also plug new code into existing classes.

# Features
1. Delegates are type-safe, object-oriented and secure.

	> Delegates are type-safe pointer of any method. 

1. Delegates in C# are similar to the function pointer in C/C++, but delegates are fully object-oriented, and unlike C++ pointers to member functions, delegates encapsulate both an object instance and a method. It provides a way which tells which method is to be called when an event is triggered.

	> For example, if you click an Button on a form (Windows Form application), the program would call a specific method. In simple words, it is a type that represents references to methods with a particular parameter list and return type and then calls the method in a program for execution when it is needed.

2. Delegates are mainly used in implementing the call-back methods and events.

	> A delegate is a solution for situations in which you want to pass methods around to other methods. Typically, we pass data as parameters to methods, however, in delegate we pass methods as parameters to methods. In this case, we can have a method to invoke other methods.
	>  
	> They are used in these senarios. We do not know at compile time what this second methods is. That information is available only at runtime hence Delegates will be used to resolve these senarios.

1. Delegates can be chained together; for example, two or more methods can be called on a single event.

1. Delegate doesn’t care about the class of the object that it references. Delegates can also be used in “anonymous methods” invocation.

# Steps
1. Declare delegates. The definition looks like abstract method declarations.

	Syntax:
	
	`[modifier] delegate [return_type] [delegate_name] ([parameter_list]);`
	
	> **modifier**: It is the required modifier which defines the access of delegate and it is optional to use.
	> 
	> **delegate**: It is the keyword which is used to define the delegate.
	> 
	> **return_type**: It is the type of value returned by the methods which the delegate will be going to call. It can be void. A method must have the same return type as the delegate.
	> 
	> **delegate_name**: It is the user-defined name or identifier for the delegate.
	> 
	> **parameter_list**: This contains the parameters which are required by the method when called through the delegate.

	e.g. 

	`public delegate int operation(int x, int y);`

	When the C# compiler encounters this line, it defines a type derived from `System.MulticastDelegate` class, that also implements a method named Invoke that has exactly the same signature as the method described in the delegate declaration:

		public class operation : System.MulticastDelegate  
		{  
		    public int Invoke(int x, int y);  
		    // Other code  
		}  

1. Instantiate delegates. After declaring a delegate, create a delegate object using `new` keyword and associate with a particular method.

	Syntax:

	`[delegate_name]  [instance_name] = new [delegate_name](calling_method_name);`

	e.g.

		operation opt = new operation(Add);
	       // here,
	       // "operation" is delegate name. 
	       // "opt" is instance_name
	       // "Add" is the calling method, can be static method or instance method

3. Make a method call to the delegate object and pass parameters to the delegated method, also receive the return value. 

	e.g.

		var returnValue = opt.Add(val1, val2);

## Demo
	using System; 
	namespace GeeksForGeeks { 
	      
	// declare class "Geeks" 
	class Geeks { 
	      
	// Declaring the delegates 
	// Here return type and parameter type should  
	// be same as the return type and parameter type 
	// of the two methods 
	// "addnum" and "subnum" are two delegate names 
	public delegate void addnum(int a, int b); 
	public delegate void subnum(int a, int b); 
	      
	    // method "sum" 
	    public void sum(int a, int b) 
	    { 
	        Console.WriteLine("(100 + 40) = {0}", a + b); 
	    } 
	  
	    // method "subtract" 
	    public void subtract(int a, int b) 
	    { 
	        Console.WriteLine("(100 - 60) = {0}", a - b); 
	    } 
	  
	// Main Method 
	public static void Main(String []args) 
	{ 
	      
	    // creating object "obj" of class "Geeks" 
	    Geeks obj = new Geeks(); 
	  
	    // creating object of delegate, name as "del_obj1"  
	    // for method "sum" and "del_obj2" for method "subtract" & 
	    // pass the parameter as the two methods by class object "obj" 
	    // instantiating the delegates 
	    addnum del_obj1 = new addnum(obj.sum); 
	    subnum del_obj2 = new subnum(obj.subtract); 
	  
	    // pass the values to the methods by delegate object 
	    del_obj1(100, 40); 
	    del_obj2(100, 60); 
	  
	    // These can be written as using 
	    // "Invoke" method 
	    // del_obj1.Invoke(100, 40); 
	    // del_obj2.Invoke(100, 60); 
	} 
	} 
	} 

Output:

	(100 + 40) = 140
	(100 - 60) = 40

> In the above program, there are two delegates addnum and subnum. We are creating the object obj of the class Geeks because both the methods(addnum and subnum) are instance methods. So they need an object to call. If methods are static then there is no need to create the object of the class.

# More
## Multicasting
Multicasting of delegate is an extension of the normal delegate(Single Cast Delegate). It helps the user to point more than one method in a single call.

1. Delegates are combined and when you call a delegate then a complete list of methods is called.
1. All methods are called in First in First Out(FIFO) order.
1. ‘+’ or ‘+=’ Operator is used to add the methods to delegates.
1. ‘–’ or ‘-=’ Operator is used to remove the methods from the delegates list.
2. multicasting of delegate should have a return type of Void otherwise it will throw a runtime exception. Also, the multicasting of delegate will return the value only from the last method added in the multicast. Although, the other methods will be executed successfully.

Demo:

	// C# program to illustrate the  
	// Multicasting of Delegates 
	using System; 
	  
	class rectangle { 
	      
	// declaring delegate 
	public delegate void rectDelegate(double height, 
	                                  double width); 
	  
	    // "area" method 
	    public void area(double height, double width) 
	    { 
	        Console.WriteLine("Area is: {0}", (width * height)); 
	    } 
	   
	    // "perimeter" method 
	    public void perimeter(double height, double width) 
	    { 
	        Console.WriteLine("Perimeter is: {0} ", 2 * (width + height)); 
	    } 
	   
	   
	// Main Method 
	public static void Main(String []args) 
	{ 
	      
	    // creating object of class  
	    // "rectangle", named as "rect" 
	    rectangle rect = new rectangle(); 
	  
	    // these two lines are normal calling 
	    // of that two methods 
	    // rect.area(6.3, 4.2); 
	    // rect.perimeter(6.3, 4.2); 
	  
	    // creating delegate object, name as "rectdele" 
	    // and pass the method as parameter by  
	    // class object "rect" 
	    rectDelegate rectdele = new rectDelegate(rect.area); 
	      
	    // also can be written as  
	    // rectDelegate rectdele = rect.area; 
	  
	    // call 2nd method "perimeter" 
	    // Multicasting 
	    rectdele += rect.perimeter;  
	  
	    // pass the values in two method  
	    // by using "Invoke" method 
	    rectdele.Invoke(6.3, 4.2); 
	    Console.WriteLine(); 
	      
	    // call the methods with  
	    // different values 
	    rectdele.Invoke(16.3, 10.3); 
	} 
	} 
Output:

	Area is: 26.46
	Perimeter is: 21 
	
	Area is: 167.89
	Perimeter is: 53.2

## Delegate vs. Interface
Both delegates and interfaces enable a class designer to separate type declarations and implementation. An interface reference or a delegate can be used by an object that has no knowledge of the class that implements the interface or delegate method. 

- A given interface can be inherited and implemented by any class or struct. 
- A delegate can be created for a method on any class, as long as the method fits the method signature for the delegate. 

Use a delegate in the following circumstances:

- An `eventing design pattern` is used. It provides a way which tells which method is to be called when an event is triggered.
- It is desirable to encapsulate a `static method`. Interface has to be applied to instances of class.
- The caller has no need to access other properties, methods, or interfaces on the object implementing the method.
- Easy composition is desired. i.e. multicasting 
- A class may need more than one `implementation of the method`.

Use an interface in the following circumstances:

- There is a group of related methods that may be called.
- A class only needs one implementation of the method.
- The class using the interface will want to cast that interface to other interface or class types.
- The method being implemented is linked to the type or identity of the class: for example, comparison methods.

一般来说，delegate 和 interface都可以实现行为定义与行为实现的分离，碰到问题时，应该使用delegate 还是 interface？

1. comparasion table

	![](/images/posts/20200214-delegate-1.png)

1. 如果行为的实现是基于对象的，也就是说，是对象自带的行为，用interface+class结构，会更清晰；如果是因为某个event动态触发的行为，用delegate+method，会更灵活。e.g.  `IComparable` or the generic version, `IComparable<T>`. IComparable declares the CompareTo method, which returns an integer that specifies a less than, equal to, or greater than relationship between two objects of the same type. IComparable can be used as the basis of a sort algorithm. Although using a delegate comparison method as the basis of a sort algorithm would be valid, it is not ideal. Because the ability to compare belongs to the class and the comparison algorithm does not change at run time, a single-method interface is ideal.
	> 这个例子中，interface 与delegate 都可以使用，但是“比较”这个行为，通常是对象自身具备的一个行为，因此用interface更合适


# References #
[Delegates (C# Programming Guide)](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/)

[When to Use Delegates Instead of Interfaces (C# Programming Guide)](https://docs.microsoft.com/en-ca/previous-versions/visualstudio/visual-studio-2010/ms173173(v=vs.100))