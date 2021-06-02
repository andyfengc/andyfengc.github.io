---
layout: post
title: C# asynchronous tutorial - Thread, multithreading, volatile
author: Andy Feng
---

# Introduction #

# lock
The lock statement acquires the mutual-exclusion lock for a given object. While a lock is held, the thread that holds the lock can acquire and release the lock. Any other thread is blocked from acquiring the lock and waits until the lock is released. 
lock 关键字将语句块标记为临界区，方法是获取给定对象的互斥锁，执行语句，然后释放该锁。lock 确保当一个线程位于代码的临界区时，另一个线程不进入临界区。如果其他线程试图进入锁定的代码，则它将一直等待（即被阻止），直到该对象被释放。

e.g.

	private readonly object x = new object();
	lock (x)
	{
	    // Your code...
	}

here, `x` is an expression of a reference type. It's precisely equivalent to

	object __lockObj = x;
	bool __lockWasTaken = false;
	try
	{
	    System.Threading.Monitor.Enter(__lockObj, ref __lockWasTaken);
	    // Your code...
	}
	finally
	{
	    if (__lockWasTaken) System.Threading.Monitor.Exit(__lockObj); // release the lock
	}

As we can see, the code uses a try...finally block, the lock is released even if an exception is thrown within the body of a lock statement.

You can't use the await operator in the body of a lock statement.

demo, We defines an Account class that synchronizes access to its private `balance` field by locking on a dedicated `balanceLock` instance. It ensures that the balance field cannot be updated simultaneously by two threads attempting to call the Debit or Credit methods simultaneously.

	async Task Main()
	{
		var account = new Account(1000);
	    var tasks = new Task[100];
	    for (int i = 0; i < tasks.Length; i++)
		{
			tasks[i] = Task.Run(() => Update(account));
		}
		await Task.WhenAll(tasks);
		Console.WriteLine($"Account's balance is {account.GetBalance()}");
		// Output:
		// Account's balance is 2000
	}
	
	static void Update(Account account)
	{
		decimal[] amounts = { 0, 2, -3, 6, -2, -1, 8, -5, 11, -6 };
		foreach (var amount in amounts)
		{
			if (amount >= 0)
			{
				account.Credit(amount);
			}
			else
			{
				account.Debit(Math.Abs(amount));
			}
		}
	}
	
	// Define other methods and classes here
	public class Account
	{
		private readonly object balanceLock = new object();
		private decimal balance;
	
		public Account(decimal initialBalance) { balance = initialBalance; }
	
		public decimal Debit(decimal amount)
		{
			if (amount < 0)
			{
				throw new ArgumentOutOfRangeException(nameof(amount), "The debit amount cannot be negative.");
			}
	
			decimal appliedAmount = 0;
			lock (balanceLock)
			{
				if (balance >= amount)
				{
					balance -= amount;
					appliedAmount = amount;
				}
			}
			return appliedAmount;
		}
	
		public void Credit(decimal amount)
		{
			if (amount < 0)
			{
				throw new ArgumentOutOfRangeException(nameof(amount), "The credit amount cannot be negative.");
			}
	
			lock (balanceLock)
			{
				balance += amount;
			}
		}
	
		public decimal GetBalance()
		{
			lock (balanceLock)
			{
				return balance;
			}
		}
	}

## FAQ
通常，应避免锁定 public 类型，否则实例将超出代码的控制范围。常见的结构 lock (this)、lock (typeof (MyType)) 和lock ("myLock") 违反此准则.

最佳做法是定义 private 对象来锁定; 或 private static 对象变量来保护所有实例所共有的数据(类似volative）。

# volatile
多个线程同时访问一个变量，CLR为了效率，允许每个线程进行本地缓存，这就导致了变量的不一致性。volatile就是为了解决这个问题，volatile修饰的变量，不允许线程进行本地缓存，每个线程的读写都是直接操作在共享内存上，这就保证了变量始终具有一致性。

volatile 关键字表示字段可能被多个并发执行线程修改。volatile 关键字是告诉C#编译器和JIT编译器，不对 volatile 标记的字段做任何的缓存。确保字段读写都是原子操作，最新值。

volatile 修饰符通常用于由多个线程访问而不使用 lock 语句对访问进行序列化的字段。

	class Test
	{
	   public volatile int i;
	   Test(int _i)
	   {
	      this.i = _i;
	   }
	} 

volatile 关键字可应用于以下类型的字段

1. 引用类型
1. 整型，如 sbyte、byte、short、ushort、int、uint、char、float 和 bool。
1. 具有整数基类型的枚举类型。
1. 已知为引用类型的泛型类型参数。
1. 不能将局部变量声明为 volatile。

# volatile vs. lock
volatile是C#的一种同步关键字，其意义是针对程序中一些敏感数据，不允许多线程同时访问，保证数据在任何访问时刻，最多有一个线程访问，以保证数据的完整性，虽与java中的synchronized 关键字有些类似相似，但是二者存在着很大的不同，最明显的地方就是volatile是修饰变量的修饰符，而synchronized是修饰一段代码或方法。

volatile使变量保证了在内存中的共享，也就是其修饰的字段没有放在工作内存（寄存器），而是被直接放在主存操作，当一个线程修改了其修饰的变量，另一个线程可以直接读到修改后的值。

![](/images/posts/20200927-multi-thread-1.png)

lock确保当一个线程位于代码的临界区时，另一个线程不进入临界区。如果其他线程试图进入锁定的代码，则它就会被阻止，直到该对象被释放。也就是说在用到临界资源，确保线程可以排队进入执行临界区中的方法。

从上我们可以看出volatile和lock的最大不同就是volatile使多个线程同时访问一个变量，每个线程的读写都是直接操作在共享内存上，从而在牺牲效率的基础上保证了变量的始终一直性，而lock大多是在牺牲效率的基础控制多个线程排队去访问一个代码块。

volatile 多数情况下很有用处的，毕竟锁的性能开销还是很大的。可以把当成轻量级的锁，根据具体场景合理使用，能提高不少程序性能。

线程中的 Thread.VolatileRead 和 Thread.VolatileWrite 是 volatile 以前的版本。

# ThreadStaticAttribute
类的静态字段在类的实例中是共享的。多个线程修改实例字段的值在对其它线程来说是可见的，这也是clr默认的行为。对静态字段添加ThreadStaticAttribute标记可以改变这种默认的行为。 [ThreadStatic] Indicates that the value of a static field is unique for each thread. 即 指示静态字段的值对于每个线程都是唯一的。用 ThreadStaticAttribute 标记的 static 字段不在线程之间共享。每个执行线程都有单独的字段实例，并且独立地设置及获取该字段的值。如果在不同的线程中访问该字段，则该字段将包含不同的值。

> 具有ThreadStatic标记的静态变量，在每个线程中都有自己的副本。而一般静态变量在进程之间共享的。

e.g. we create 2 fields - one is static and another one is ThreadStatic.

![](/images/posts/20200927-multi-thread-2.png)

![](/images/posts/20200927-multi-thread-3.png)

As we can see, static variable is shared among threads whereas ThreadStatic variable is unique to each thread and not shared.

ThreadLocal<T> which is similar to ThreadStatic

# static

# static vs. volatile
static指的是类的静态成员，实例间共享。static是Object之间共享的变量；static变量可能会被线程cache；static可以修饰volatile
> static是基于class的，不管这个class创建了多少objects，或不创建object，static变量都是存在的
> 如果两个线程读取一个class的static变量，那么这两个线程会在各自cache中生成该static对象的本地copy，然后分别进行更新，彼此独立
> 更新一个static对象，会立即反应在同一个线程环境中的多个class objects上，但不会反映在跨线程的多个objects，因为其他线程已经有一个copy了，某个线程对该static对象的更改不会反应在其他线程上

volatile是线程之间共享的变量；volatile跟内存模型有关，volatile 变量始终存放在主内存，线程执行时会将变量从主内存加载到线程工作内存，建立一个副本，在某个时刻写回。valatile指的每次都读取主内存的值，有更新则立即写回主内存。
> 声明volatile变量，能确保多线程环境下，各个线程不会cache对象，而是始终从主内存读取
> 更新一个volatile变量，会立即反应在其他线程上


![](/images/posts/20200927-multi-thread-4.png)



# References 
[lock statement (C# reference)](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/lock-statement)

[volatile (C# Reference)](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/volatile)

[https://www.c-sharpcorner.com/article/overview-of-threadstatic-attribute-in-c-sharp/](https://www.c-sharpcorner.com/article/overview-of-threadstatic-attribute-in-c-sharp/)

