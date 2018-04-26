---
layout: post
title: Command & Rollback design pattern practice
author: Andy Feng
---

# Outline #

- Introduction
- Mamonto pattern
	- Workflow
	- Benefits
	- Implementation steps
	- Sample code
- Command pattern
	- Workflow
	- Benefits
	- Implementation steps
	- Sample code
- Comparison
- A comprehensive demo

# Introduction #
In typical software structure, the invoker(caller) and receiver(implementation) are coupled. Invoker(A) calls receiver(B) to do some work. In this case, A initiates an instance of B and keeps a reference of b. However, if we want to record/undo/redo/transact the communication(i.e. request or command) from A to B. The coupled association cannot archive this objective. Therefore, we need to decouple the invoke either via IOC container or go further to use some design patterns to record the communication between invoker and receiver.

![](/images/posts/20180423-command-pattern-2.png)

Command/Rollback are important features for application to improve user experience. They enable us to encapsulate commands or instructions into objects, then we can easily repeat commands or get back to previous status. We can even persist commands into database and restore later on. Therefore, it is feasible for us to record the request commands from invoker to receiver, and restore an object or multiple associated project to previous states.

Below design patterns can help us achieve this objective:

- memento pattern
- command pattern

# Memonto pattern #
The memento pattern is implemented with three objects: the originator, a caretaker and a memento.

- `originator` is an object that has an internal status. 
	> - originator creates a memento containing a snapshot of its current internal state
	> - It uses the memento to restore its internal state

- `caretaker` is an object to manage rollback. It can do something with the originator and also able to undo the change.
	> - caretaker(client) can request a memento from the originator(to save the internal state of the originator)
	> - caretaker keeps a list of mementos and maintains mementos like save points. It never operates on or examines the contents of a memento.
	> - caretaker can pass a memento back to the originator (to restore to a previous state)

- `memento` is an opaque object saving the that originator's state
	> - memento includes the properties of originator which represents originator's state

## Workflow ##
![](/images/posts/20180420-memento-pattern-1.png)

Steps:

1. The caretaker first asks the originator for a memento object. The memento object saves originator's internal state.
1. Then user does whatever operation (or sequence of operations) on originator. 
2. To roll back to the state before the operations, caretaker returns the memento object to the originator.
3. The originator restores to a previous status from the memento object

## Benefits ##
The benefits of Memonto pattern are:

- We can save and restore the internal state of an originator without violating its encapsulation of object(originator)
- The internal state of an object can be saved externally and the object can be restored to this state later.

## Implementation steps ##
here is the class diagram which represents the code structure:

![](/images/posts/20180420-memento-pattern-2.gif)

To implement this pattern, we need:

1. Add two additional methods to original class. i.e. service class. It works as Originator
	
	- SetState() - for saving state
	- GetState() - for restoring state

1. Add code to invoke above methods either saving or restoring state to the class which calls original service,  i.e. Controller, or Console or other clients. It works as CareTaker class.

1.  add a new class Memento to encapsulate properties of original class.

## Sample code ##
Here we have a simple sample. In this example, state of Memonto object is regarded as a simple string. In actual application, it could be an enum or properties of original object. Memento object includes data represents the object's state. 

Originator.java

	public class Originator {
	    private String state;
	    ...
	 
	    public void set(String state) {
	        this.state = state;
	        System.out.println("Originator: Setting state to " + state);
	    }
	 
	    public Memento saveToMemento() {
	        System.out.println("Originator: Saving to Memento.");
	        return new Memento(this.state);
	    }
	 	// restore from memento
	    public void setMemento(Memento memento) {
	        this.state = memento.getSavedState();
	        System.out.println("Originator: State after restoring from Memento: " + state);
	    }
	}

Memento

	public class Memento {
	    private final String state;
	
	    public Memento(String stateToSave) {
	        state = stateToSave;
	    }
	
	    // accessible by outer class only
	    private String getSavedState() {
	        return state;
	    }
	}

Caretaker

	import java.util.List;
	import java.util.ArrayList;	
	public class Caretaker {
	    public static void main(String[] args) {
	        List<Originator.Memento> savedStates = new ArrayList<Originator.Memento>();
	 
	        Originator originator = new Originator();
	        originator.set("State1");
	        originator.set("State2");
	        savedStates.add(originator.saveToMemento());
	        originator.set("State3");
	        // We can request multiple mementos, and choose which one to roll back to.
	        savedStates.add(originator.saveToMemento());
	        originator.set("State4");
	 
			// restore from Memento
	        originator.setMemento(savedStates.get(1));   
	    }
	}

Output: 

	Originator: Setting state to State1
	Originator: Setting state to State2
	Originator: Saving to Memento.
	Originator: Setting state to State3
	Originator: Saving to Memento.
	Originator: Setting state to State4
	Originator: State after restoring from Memento: State3

Improved Originator:

	public class Originator
	{	
	    public string String1 { get; set; }
	    public string String2 { get; set; }
	
	    public OriginalObject(string str1, string str2)
	    {
	        this.String1 = str1;
	        this.String2 = str2;
	    }

		public Memento saveToMemento() {
	        System.out.println("Originator: Saving to Memento.");
	        return new Memento(this.String1, this.String2);
	    }
	
	    public void Revert(Memento memento)
	    {
	        this.String1 = memento.string1;
	        this.String2 = memento.string2;
	    }
	}

Improved Memento, it keeps all properties of original object

	public class Memento
	{
	    public readonly string string1;
	    public readonly string string2;
	
	    public Memento(string str1, string str2)
	    {
	        this.string1 = str1;
	        this.string2 = str2;
	    }
	}

# Command pattern #

Command pattern includes an object encapsulates all information needed to perform an action or trigger an event at a later time. This information includes the method name, the object(command) that owns the method and values for the method parameters.

Comman pattern is implemented by four objects:

- `Invoker` knows how to execute a command and saves the command execution.
	> invoker does not know anything about a concrete command, it knows only about the command interface
- `Command` knows about receiver and invokes a method of the receiver. The command stores values of parameters of receiver method. It has a execute() method
	> `Command` consists of Command interface/abstract and concrete command objects
- `Receiver` is the object do the actual work when the execute() method in command is called
- `Client` holds invoker object(s), command objects and receiver objects. The client decides which receiver objects it assigns to the command objects, and which commands it assigns to the invoker. The client decides which commands to execute at which points. To execute a command, it passes the command object to the invoker object.

Before using command pattern, we have

![](/images/posts/20180423-command-pattern-4.png)

Here, client object creates receiver and pass it to invoker object. Then, client call invoker to do some work.

After using command pattern, we will have

![](/images/posts/20180423-command-pattern-5.png)

Here, client creates receiver and command objects. Then, client setup how command object call receiver object to implement some actions. Finally, client setup invoker to utilize command object. Please note that command object is like an agent between invoker and receiver and it records the request between them. Command object can be persisted, undo and redo.

We use command pattern in below cases:
- We need to support logging.
- A history management is needed (that is, undo/redo operations).
- We need to support transactions.
- We need callback functionality.
- Requests need to be handled at variant times and (or) in variant orders (using queue, stack, etc.).
- Source of the request should be decoupled from the object that actually handles the request.

## Workflow ##
![](/images/posts/20180423-command-pattern-1.jpg)

## Benefits ##
Command pattern define separate command objects that encapsulate a request. 

- It enables us to configure a class with a command object that is used to perform a request. The class is no longer coupled to a particular request and has no knowledge (is independent) of how the request is carried out.
- Using command objects makes it easier to construct general components that need to delegate, sequence or execute method calls. Command objects don't need to know the class of the method or the method parameters. 
- Using an invoker object allows bookkeeping about command executions when commands performed, as well as implementing different modes for commands, which are managed by the invoker object, without the need for the client to be aware of the existence of bookkeeping or modes.

## Implementation steps ##
![](/images/posts/20180423-command-pattern-3.png)

or

![](/images/posts/20180423-command-pattern-6.png)

To implement command pattern, we need:

1. Add an additional command interface/concrete class between invoker and receiver. Command interface has a execute() method as convention.

1. Client creates a receiver object and passes it to command object. Receiver object encapsulate the logic and command.execute() calls receiver.method().
 
1. Client passes the command object to invoker object. Invoker object call command.execute() to do the actual work.

1. Client calls invoker object.

## Sample code ##
Command interface: 

	abstract class Command {  
	    public abstract void execute();  
	} 

Concrete command class:

	class ConcreteCommand extends Command {  
	    private Receiver receiver;  
	    public ConcreteCommand(Receiver receiver){  
	        this.receiver = receiver;  
	    }  
	    public void execute() {  
	        this.receiver.doSomething();  
	    }  
	}  

Receiver interface (i.e. sub service interface in real world):

	interface IReceiver {  
	    void doSomething();
	}  

Receiver implementation (i.e. sub service class in real world):

	class Receiver : IReceiver{  
	    public void doSomething(){  
	        System.out.println("I am doing something...");  
	    }  
	}

Invoker class (i.e. service class in real world)

	class Invoker {  
	    private Command command;  
	    public void setCommand(Command command) {  
	        this.command = command;  
	    }  
	    public void action(){  
	        this.command.execute();  
	    }  
	} 

Client (i.e. Controller, Console or other clients):

public class Client {  
    public static void main(String[] args){  
        Receiver receiver = new Receiver();
		// setup command object(s)
        Command command = new ConcreteCommand(receiver); 
		// setup invoke object
        Invoker invoker = new Invoker();  
        invoker.setCommand(command);  
		// call command to implement receiver's logic
        invoker.action();  
    }  
}  

We can go further to add a macro command class to record a list of commands.

macro command interface:

	public interface MacroCommand extends Command {
	    public void add(Command cmd);
	    public void remove(Command cmd);
	}

macro command class:

	public class MacroAudioCommand implements MacroCommand<string> {
	    
	    private List<Command> commandList = new ArrayList<Command>();
	    @Override
	    public void add(Command cmd) {
	        commandList.add(cmd);
	    }
	    @Override
	    public void remove(Command cmd) {
	        commandList.remove(cmd);
	    }
	    @Override
	    public void execute() {
	        for(Command cmd : commandList){
	            cmd.execute();
	        }
	    }
	}

We change client, instead of call command separately, we use macro command to call a batch of commands

	MacroCommand marco = new MacroAudioCommand();
	var command1 = new CommandImpl(new Receiver1());
 	var command2 = new CommandImpl(new Receiver2());
	var command3 = new CommandImpl(new Receiver3());   
    marco.add(command1);
    marco.add(command2);
    marco.add(command3);
    invoker.setCommandList(macro);
	invoker.action();

# Comparison #
1. Memento is a way to store state so that it is restoreable (undo). Command captures all information needed to perform some certain actions (not necessarily `undo` operation, can be redo/add to transaction)

1. Memento pattern saves x states of an object. Command pattern records x requests(actions) between invoker and receiver. 

	For example, consider a game of chess where we'd like to be able to revert last x moves.
	> One way to do it would be to record all moves. We could then either "undo" moves. Or simple "replay" the first n-x moves. That would be the Command approach.
	> 
	> Another way would be to save the last x states of the game (and be able to restore them). This is the Memento approach.

1. Memento is static and focus on state. Command is dynamic and focus on action or execution (undo/redo).

1. Memento pattern implements snapshot feature. Each memento object represents a snapshot of properties of original object. Command pattern implements record/undo/redo feature. Each command object encapsulates a request details between invoker and receiver.

1. Usually, command pattern is more powerful than Memento pattern. we can use Command pattern.

# A comprehensive demo #
Senerio:

We have an application to swap license of users. A swap operation includes remove a existing license and grant a pending license. Therefore, it could be regarded as a command list including two commands. The reason we use command to replace direct invoking because we can add additional operations on a command, such as persist command, restore command, undo and redo commands and so on. 

Here is the major models of this senario:  

![](/images/posts/20180423-command-pattern-7.png)

- `Controller` - client
- `Swap service` - invoker
- `Command`
- `License service` - receiver
- `Request` - entity

Typically, Command and Memento patterns are behaviour pattern and they work in runtime. Here, we hope to move on to persist Commands into database so that we can restore actions on request objects later on. This is the class diagram for the implementation:

![](/images/posts/20180423-command-pattern-8.png)

Here,

- `Task` - command list
- `TaskType` - predefiend task action. It enables us to restore the right receiver service(s) for task.
- `TaskCommand` - command
- `CommandType` - predefined command actions to let us restore command object(s) and generate right association between Command and Receiver service. It can be regarded as the exact methods of receiver object(s) which will be are called by command object(s).
- `Request` - existing data entity. It can be replaced by any specific models that command manipulates.

In programming, we also need to add an additional service (TaskService) to play as invoker and restored receiver services(s) - LicenseService to implement the actual logic. 

![](/images/posts/20180423-command-pattern-9.png)

# References #
[Memento pattern](https://en.wikipedia.org/wiki/Memento_pattern)

[Command pattern](https://en.wikipedia.org/wiki/Command_pattern)
