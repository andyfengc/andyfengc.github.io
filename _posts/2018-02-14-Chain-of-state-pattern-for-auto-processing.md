---
layout: post
title: Chain of state pattern for auto processing
author: Andy Feng
---

# Introduction #
## State pattern  ##
[State pattern](https://en.wikipedia.org/wiki/State_pattern) is a behavioral software design pattern. It implements a state machine in object-oriented design. State pattern can be used to maintain various states in our program. It allows an object to change its behavior when it’s internal state changes. The object will appear to change its class.

State pattern is a practice of [OCP](https://en.wikipedia.org/wiki/Open/closed_principle) principle. It is closed for object and open for states. It can help us to build maintainable code.

Here is the state pattern architecture of [Gang of Four patterns](https://en.wikipedia.org/wiki/Design_Patterns).

![](/images/posts/20180213-state-2.png)

The participants in above UML diagram are:

- **Context**: The context class is accessed by the client (users or external application). It holds the object of concrete state object that changes its state; therefore, state of context class object is also changed.
- **State**: This is an interface which defines the behavior associated with a state of the context object. 
- **Concrete State**: It implements the state interface. This class represents the state of the context object and actual implementation of behavior. Each concrete state class implements a behavior associated with a state of context.

## Senario ##
There is a company need to manage user account for employees. When new employee coming, they have to request a new account and awaiting for being granted. Let's suppose we have a simple sequence state machine diagram:

![](/images/posts/20180213-state-4.png)

# Solution of state pattern #
1. Define a context object to manage states. It represents the object to manage states. In this case, it simply keeps the current status.

	    public class Context
	    {
	        // current state
	        public IState state;
	
	        public Context()
	        {
	        }
	
	        public void SetState(IState state)
	        {
	            this.state = state;
	        }
	
	        public IState GetState()
	        {
	            return this.state;
	        }
	    }

1. Define a state interface

	    public interface IState
	    {
	        void Process(Context context);
	        string GetName();
	    }

1. Create multiple states to implement the interface, each concrete state class includes:

	- the logic to make some outgoing processing
	- call context object to transit to next state

	    public class RequestedState : IState
	    {
	        public string GetName()
	        {
	            return "Requested";
	        }
	
	        public void Process(Context context)
	        {
	            System.Console.WriteLine("account requested...");
	            context.SetState(new ApprovedState());
	        }
	    }
	
	    public class ApprovedState : IState
	    {
	        public string GetName()
	        {
	            return "Approved";
	        }
	
	        public void Process(Context context)
	        {
	            System.Console.WriteLine("account approved...");
	            context.SetState(new GrantedState());
	        }
	    }
	
	    public class GrantedState : IState
	    {
	        public string GetName()
	        {
	            return "Granted";
	        }
	
	        public void Process(Context context)
	        {
	            System.Console.WriteLine("account granted...");
	            context.SetState(new RemovedState());
	        }
	    }
	
	    public class RemovedState : IState
	    {
	        public string GetName()
	        {
	            return "Removed";
	        }
	
	        public void Process(Context context)
	        {
	            System.Console.WriteLine("account removed...");
	        }
	    }

	On the other hand, we can also reverse the sequence, that is, each concret state class includes:

	- the logic to make incoming processing
	- call context object to transit to this state 

1. Test. Let's create 3 context objects, set initial states, then let the state objects make transition for us. Here, we create a simple console app for demo purpose:

            // context starts from requested state
            var context = new Context();
            var state = new RequestedState();
            context.SetState(state); // set init state
            // start process
            System.Console.WriteLine("current state of context: " + context.GetState().GetName());
            state.Process(context);
            System.Console.WriteLine("current state of context: " + context.GetState().GetName());
            System.Console.WriteLine();

            // context2 starts from granted state
            var context2 = new Context();
            var state2 = new GrantedState();
            context2.SetState(state2);// set init state
            // start process
            System.Console.WriteLine("current state of context2: " + context2.GetState().GetName());
            state2.Process(context2);
            System.Console.WriteLine("current state of context2: " + context2.GetState().GetName());
            System.Console.WriteLine();

            // context keep the removed state
            var context3 = new Context();
            var state3 = new RemovedState();// set init state
            context3.SetState(state3);
            // start process
            System.Console.WriteLine("current state of context3: " + context3.GetState().GetName());
            state3.Process(context3);
            System.Console.WriteLine("current state of context3: " + context3.GetState().GetName());

	We got

	![](/images/posts/20180213-state-1.png)

As we can see, the benefit is all transition details were encapsulated into states. The logic is decoupled from major context object. 

In typical state pattern, if the context object transits from one state to another, it remains in new state and never moves on. However, what if we hope to the context object can automatically transits along the workflow of state machine diagram. What should we do?   

# Chain of state pattern #

1. We can chain them together by automatically move on when the major context object update to new state

    public class Context
    {
        // current state
        public IState state;

        public Context()
        {
        }

        public void SetState(IState state)
        {
            this.state = state;
            this.Moveon(); // add a new method
        }

        public void Moveon()
        {
            this.state.Process(this);
        }

        public IState GetState()
        {
            return this.state;
        }
    }

1. do not change state classes

1. update the test code and run

		// context starts from requested state
		var context = new Context();
		var state = new RequestedState();
		// start process
		System.Console.WriteLine("current state of context: " + state.GetName());
		context.SetState(state); // set init state and move on automatically
		System.Console.WriteLine("current state of context: " + context.GetState().GetName());
		System.Console.WriteLine();
		
		// context2 starts from granted state
		var context2 = new Context();
		var state2 = new GrantedState();
		// start process
		System.Console.WriteLine("current state of context2: " + state2.GetName());
		context2.SetState(state2);// set init state and move on automatically
		System.Console.WriteLine("current state of context2: " + context2.GetState().GetName());
		System.Console.WriteLine();
		
		// context keep the removed state
		var context3 = new Context();
		var state3 = new RemovedState();
		// start process
		System.Console.WriteLine("current state of context3: " + state3.GetName());
		context3.SetState(state3);// set init state and move on automatically
		System.Console.WriteLine("current state of context3: " + context3.GetState().GetName());

	we got

	![](/images/posts/20180213-state-6.png)


# More #
## When to use state pattern ##
If we have fewer objects but with multiple states, also the object behave differently in different states, please consider state pattern. It models each state to an object and helps us encapsulate the logic into states rather than use many if-else statement inside the object.

State pattern moves the behavior methods from object for an abstract state class(or interface), then creates multiple states to inherit or implement the abstract state class. In this approach, behavior is decoupled from context object and moved to separate state objects.

## More complex states ##
In this case, for simplicity, all of state nodes are stateless and it is a simple sequence. In real world, for each state class, we might query database or external webservices to determine the logic and transit to the proper state(s). 

For instance, we have a more complex state machine diagram as below. There might have multiple transitions from one state to another. 

![](/images/posts/20180213-state-5.png)

We just need to add additional logic in each state class to handle the complex senario. We can transit to different states based on different logic in each state.

# State pattern vs. Chain of responsibility pattern #

[Chain-of-responsibility pattern](https://en.wikipedia.org/wiki/Chain-of-responsibility_pattern) creates a chain of receiver objects for a request. 

![](/images/posts/20180213-state-3.png)

Both State pattern and Chain of responsibility pattern can trigger the transitions of control. But they are different.

- State pattern is designed for a single object. Of course, we can use a single object with multiple if-else statements. States set up transitions by themselves.

- Chain of responsibility pattern is designed for handle multiple users with distinct hierachy. External clients set up the transitions.

more on [http://blog.csdn.net/zhuojiajin/article/details/17138061](http://blog.csdn.net/zhuojiajin/article/details/17138061)

# Reference #
[http://gyanendushekhar.com/2016/11/05/state-design-pattern-c/](http://gyanendushekhar.com/2016/11/05/state-design-pattern-c/)

[https://sourcemaking.com/design_patterns/state](https://sourcemaking.com/design_patterns/state)

[http://javarevisited.blogspot.ca/2014/04/difference-between-state-and-strategy-design-pattern-java.html](http://javarevisited.blogspot.ca/2014/04/difference-between-state-and-strategy-design-pattern-java.html)

[https://www.cnblogs.com/java-my-life/archive/2012/06/08/2538146.html](https://www.cnblogs.com/java-my-life/archive/2012/06/08/2538146.html)
