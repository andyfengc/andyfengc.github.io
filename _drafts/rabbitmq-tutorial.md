---
layout: post
title: RabbitMQ Tutorial
author: Andy Feng
---

# Introduction #
`RabbitMQ` is an open source message broker written in Java and follows Apache license. It is an implementation of JMS and sits between application and allows them to communicate asynchronously. 
> JMS is a specification that allows development of message-based systems. 

![](/images/posts/20221208-rabbitmq-1.jpg)

# Installation
1. Install Erlong runtime at [here](https://www.erlang.org/downloads)

	![](/images/posts/20221208-rabbitmq-2.jpg)

2. Install RabbitMQ server at [here](https://www.rabbitmq.com/install-windows.html#installer)
	> By default, RabbitMQ works as windows service. 
	
	![](/images/posts/20221208-rabbitmq-3.jpg)

3. verify installation at [http://rabbitmq:15672/](http://rabbitmq:15672/)
	![](/images/posts/20221208-rabbitmq-4.jpg)

# Develop
1. Create Maven project > modify pom.xml > add rabbitmq lib

1. Create Producer which send message to RabbitMQ Queue.

		import java.io.IOException;
		import java.util.Scanner;
		import java.util.concurrent.TimeoutException;
		
		import com.rabbitmq.client.Channel;
		import com.rabbitmq.client.Connection;
		import com.rabbitmq.client.ConnectionFactory;
		
		public class Producer {
		   private static String QUEUE = "MyFirstQueue";
		
		   public static void main(String[] args) throws IOException, TimeoutException {
		      ConnectionFactory factory = new ConnectionFactory();
		      factory.setHost("localhost");
		      try (Connection connection = factory.newConnection();
		         Channel channel = connection.createChannel()) {
		         channel.queueDeclare(QUEUE, false, false, false, null);
		
		         Scanner input = new Scanner(System.in);
		         String message;
		         do {
		            System.out.println("Enter message: ");
		            message = input.nextLine();
		            channel.basicPublish("", QUEUE, null, message.getBytes());
		         } while (!message.equalsIgnoreCase("Quit"));
		      }
		   }
		}

	> Producer class creates a connection, creates a channel, connects to a queue. User send message to the queue using basicPublish() method. If user enters quit, then application terminates.

1. Create Subscriber which receives message from RabbitMQ queue

		public class Subscriber {
		   private static String EXCHANGE = "MyExchange";
		   public static void main(String[] args) throws IOException, TimeoutException {
		      ConnectionFactory factory = new ConnectionFactory();
		      factory.setHost("localhost");
		      Connection connection = factory.newConnection();
		      Channel channel = connection.createChannel();
		      channel.exchangeDeclare(EXCHANGE, "fanout");
		
		      String queueName = channel.queueDeclare().getQueue();
		      channel.queueBind(queueName, EXCHANGE, "");
		      System.out.println("Waiting for messages. To exit press CTRL+C");
		
		      DeliverCallback deliverCallback = (consumerTag, delivery) -> {
		         String message = new String(delivery.getBody(), StandardCharsets.UTF_8);
		         System.out.println("Received '" + message + "'");
		      };
		      channel.basicConsume(queueName, true, deliverCallback, consumerTag -> { });
		   }
		}

	> Subscriber class creates a connection, creates a channel, declares the exchange, create a random queue and binds it with the exchange and then receives message from topic if there is any. Press Ctrl + C to terminate else it will keep polling queue for messages.


# References

