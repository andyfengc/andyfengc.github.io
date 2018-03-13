---
layout: post
title: Moq mocking tutorial
author: Andy Feng
---

# Introduction #
Mocking technique is very useful for testing purpose. Moq is a third party framework that enables us to create the dummy class and dummy implementation. This is very useful in the scenarios where we want to test the functionality of a class without implementing the other classes and their methods. 

# Installation #
1. create a service project and a unit test project

1. nuget > Moq > install

	![](/images/posts/20180312-moq-1.png)

# Steps #
1. Create a mock object `var mockObj = new Mock<MockClass>()`

1. Set up the mock object

	two major methods

	1. specific value

			// set up the mock by specifying argument values
			mockObj.Setup(x => x.method(param1, ...)).Returns(xxx)

	1. Moq's argument matching technique

			// specify the param of the mock should be a certain data type
			mockObj.Setup(x => x.method(It.IsAny<Param1DataType>(), ...)).Returns(xxx)
	
			// specify the param of the mock must meet some conditions
			mockObj.Setup(x => x.IsValid(It.Is<Param1DataType>(param1 => param1..condition...)).Returns(xxx)
	
			// specify the param of the mock must be within a set
			mockObj.Setup(x => x.IsValid(It.IsIn("xx", "yy", "zz"))).Returns(xxx) 
	
			// specify the param of the mock must be within a range
			mockObj.Setup(x => x.IsValid(It.IsInRange("xx", "yy", "zz", Range.Inclusive))).Returns(xxx) 
	
			// specify the param of the mock must meet a regex
			mockObj.Setup(x => x.IsValid(It.IsRegex("[xx|yy|zz]", RegexOptions.None))).Returns(xxx) 

1. Inject the mock object via constructor or method parameter to business logic. Then, make the test

		var result = BusinessServiceClassInstance.get(mockObj.Object);
		Assert.IsTrue(result);

# Demo 1 - Make uniting testing with mock via AAA principle #

In service project 

1. Develop some service logic

		public interface IMailClient
	    {
	        string Server { get; set; }
	        string Port { get; set; }
	
	        bool SendMail(string from, string to, string subject, string body);
	    }
	
	    public interface IMailer
	    {
	        string From { get; set; }
	        string To { get; set; }
	        string Subject { get; set; }
	        string Body { get; set; }
	
	        bool SendMail(IMailClient mailClient);
	    }
		
		// logic
	    public class DefaultMailer : IMailer
	    {
	        public string From { get; set; }
	        public string To { get; set; }
	        public string Subject { get; set; }
	        public string Body { get; set; }
	
	        public bool SendMail(IMailClient mailClient)
	        {
	            return mailClient.SendMail(this.From, this.To, this.Subject, this.Body);
	        }
	    }

In unit test project

1. Create a unit test class

1. Arrange - Create a mock object. Pass the mock to the logic

		// create mock object
		var mockMailClient = new Mock<IMailClient>();
		// setup mock object, we expect client.SendMail() method to be called
		mockMailClient.Setup(client =>
                client.SendMail(It.IsAny<string>(), It.IsAny<string>(), It.IsAny<string>(), It.IsAny<string>())
                );

1. Act- Execute the logic with mock
		// create mock data
		var mailer = new DefaultMailer() { From = "from@mail.com", To = "to@mail.com", Subject = "Using Moq", Body = "Moq is awesome" };
		// use mock to send email
		mailer.SendMail(mockMailClient.Object);

1. Assert - Verify the system interaction
		// verify client.SendMail() method is called at least once
		mockMailClient.Verify(client => client.SendMail(It.IsAny<string>(), It.IsAny<string>(), It.IsAny<string>(), It.IsAny<string>()), Times.AtLeastOnce);

The complete unit test code

	    [TestClass]
	    public class MoqTest
	    {
	        [TestMethod]
	        public void SendEmail_ShouldSucceed()
	        {
	            // create mock
	            var mockMailClient = new Mock<IMailClient>();
	            mockMailClient
	                .SetupProperty(client => client.Server, "server.mail.com")
	                .SetupProperty(client => client.Port, "1000");
	            // set up mock
	            mockMailClient.Setup(client =>
	                client.SendMail(It.IsAny<string>(), It.IsAny<string>(), It.IsAny<string>(), It.IsAny<string>())
	                )
	                .Callback(new Action<string, string, string, string>((a, b, c, d) =>
	                {
	                    System.Console.WriteLine($"from: {a} -> to: {b}, subject: {c}, body: {d}");
	                }))
	                .Returns(true);
	            // create mock data
	            var mailer = new DefaultMailer() { From = "from@mail.com", To = "to@mail.com", Subject = "Using Moq", Body = "Moq is awesome" };
	            // use mock to send email
	            var result = mailer.SendMail(mockMailClient.Object);
	            // verify result
	            mockMailClient.Verify(client => client.SendMail(It.IsAny<string>(), It.IsAny<string>(), It.IsAny<string>(), It.IsAny<string>()), Times.AtLeastOnce);
	            System.Console.WriteLine($"sent result: {result}");
	        }
	    }

# FAQ #
1. loose mock vs. strict mock

	By default, Moq use loose mock strategy for every mock object and it saves lines of code. If we specify a mock object as strict mode, each method of the mock object must has a corresponding Setup() method

		var mockObj = new Mock<IMailClient>(MockBehavior.Strict);

	Generally, we prefer loose mock as strict mock might break whenever we modidy the unit testing.

# References #
[Moq documentation](http://www.nudoq.org/#!/Projects/Moq)