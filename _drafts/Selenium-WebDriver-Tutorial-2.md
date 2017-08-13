---
layout: post
title: Grab and parse data using Selenium WebDriver Part 2 - Java and C# implementations
author: Andy Feng
---

This article introduces how to use Selenium WebDriver to simulate browser operations such as load a page, manipulate DOM, extract data and provides ready-to-go sample code in Java and C#. 

## Introduction ##
This is Part 2 of Selenium tutorial.

In Part 1, the basics of Selenium WebDriver is introduced. A complete demo of loading a web page, locate an element and extract data is explained. Based on demo 1, you will go further to implement a practical automation of browser. In this part, you will pragmatically open a browser instance and simulate a simple search functionality.

- demo 2, manipulate DOM within browser using java
	- create a empty java project
	- import required libraries
	- implement search

- demo 3, manipulate DOM within browser using in c#
	- create a empty .net project in visual studio
	- install required library
	- implement search

- demo 4, manipulate DOM without browser

### demo 2, manipulate DOM within browser using java ###

#### create a empty java project ####
Please use the wizard in File > new project to create a project.

#### import required libraries ####

way1, download offline libraries at [here](http://www.seleniumhq.org/download/) and import into this project.

way2, using maven to add library dependency. 

First, Right click project > configure > convert to maven project. The conversion dialog will be opened. Click ok to confirm. 

![](/images/20151104-to-maven-2.png)

A new file - pom.xml is created. It is the configuration file for Maven. 

Second, double-click to open pom.xml and switch to source code mode. Add following dependency.

    <project>
    	...
    	<dependencies>
    		<dependency>
    			<groupId>org.seleniumhq.selenium</groupId>
    			<artifactId>selenium-java</artifactId>
    			<version>2.48.0</version>
    		</dependency>
    	</dependencies>
    </project>

Lastly, build this new project and the library will be automatically downloaded.

#### Implement online search in browser ####
Selenium has build-in firefox driver. You can download Chrome driver at [here](http://chromedriver.storage.googleapis.com/index.html) and IE driver at [here](http://selenium-release.storage.googleapis.com/index.html). Run the installer and follow the steps to set up drivers.

Here is the sample via Chrome driver.

	// locate chrome driver
	String chromeDriverPath = System.getProperty("user.dir") + "/lib/chromedriver.exe";
	System.setProperty("webdriver.chrome.driver", chromeDriverPath);
	// create new web driver instance
	WebDriver driver = new ChromeDriver();
	// load google search page
	driver.get("http://www.google.ca");
	Thread.sleep(5000); // wait for a while so that we can see the page
	// locate search textbox
	WebElement searchBox = driver.findElement(By.name("q"));
	// enter search keyword
	searchBox.sendKeys("ChromeDriver");
	// search now
	searchBox.submit();
	Thread.sleep(5000); // wait for a while so that we observe
	// exit
	driver.quit();

### Demo 3, manipulate DOM within browser using in c# ###
#### install selenium WebDriver ####
The easiest way to install Selenium WebDriver library is through Nuget. Open Nuget Package Manager, search "selenium webdriver" and click install base API. Then, search "selenium webdriver chrome" to install driver.

![](/images/20151104-nuget-selenium.png)

#### Implement search ####

	// initialize a WebDriver instance
    IWebDriver driver = new RemoteWebDriver(DesiredCapabilities.HtmlUnit());
    // load google search page
    driver.Navigate().GoToUrl("https://www.google.ca");
    // print title
    Console.WriteLine("Page title: " + driver.Title);
    // enter search word and submit
    IWebElement element = driver.FindElement(By.Name("q"));
    element.SendKeys("Cheese");
    element.Submit();
    // print title
    Console.WriteLine("Page title: " + driver.Title);
    // quit the driver
    driver.Quit();	

### Demo 4, manipulate DOM without browser ###

In demo 2 and demo 3, when we run the application, it will automatically start a browser window, then the search textbox will be automatically selected and entered some text. Next, the submitted button will be clicked and search result page is returned. All of these operations are implemented through commands. However, the drawback is that the window must appear, which makes it unsuitable for run in the server. 

There are some web driver that does allow non window operations and it is nice to implement some silent tasks.

#### way1 - via HtmlUnitDriver ####
HtmlUnitDriver is a fast and lightweight implementation of WebDriver, which is based on [HtmlUnit library](http://htmlunit.sourceforge.net/). It does not open browser window and faster than open a factual window. 

Here is the sample Java code to utilize HtmlUnitDriver:

	// initialize a WebDriver instance
	WebDriver driver = new HtmlUnitDriver();
	// load google search page
	driver.get("https://www.google.ca");
	// print title
	System.out.println("Page title: " + driver.getTitle());
	// enter search word and submit
	WebElement element = driver.findElement(By.name("q"));
	element.sendKeys("Cheese");
	element.submit();
	// print title
	System.out.println("Page title: " + driver.getTitle());
	// quit the driver
	driver.quit();

Here is the version of C# code:

	// initialize a WebDriver instance
    IWebDriver driver = new RemoteWebDriver(DesiredCapabilities.HtmlUnit());
    // load google search page
    driver.Navigate().GoToUrl("https://www.google.ca");
    // print title
    Console.WriteLine("Page title: " + driver.Title);
    // enter search word and submit
    IWebElement element = driver.FindElement(By.Name("q"));
    element.SendKeys("Cheese");
    element.Submit();
    // print title
    Console.WriteLine("Page title: " + driver.Title);
    // quit the driver
    driver.Quit();

However, it does not support Javascript very well. It there are multiple js scripts, HtmlUnitDriver might fail to capture content.

#### way2 - via PhantomJS ####
PhantomJS is a headless WebKit scriptable engine with a JavaScript API. It does not required browser window. and it has fast and native support for various web standards: DOM handling, CSS selector, JSON, Canvas, and SVG. PhantomJS is very suitable for run in the server side.

##### Install phantomjs in Java #####
Download driver server at http://phantomjs.org/download.html

Add library at [http://mvnrepository.com/artifact/com.codeborne/phantomjsdriver/1.2.1](http://mvnrepository.com/artifact/com.codeborne/phantomjsdriver/1.2.1 "here") or add dependency at maven
    
	<dependency>
			<groupId>com.codeborne</groupId>
			<artifactId>phantomjsdriver</artifactId>
			<version>1.2.1</version>
	</dependency>

##### Apply phantomjs in Java #####
Now you will create a driver instance. Make a search request from google and print some content without opening a browser. 

    // Create a new instance of the phantomjs driver
	System.setProperty("phantomjs.binary.path", System.getProperty("user.dir") + "/lib/phantomjs.exe");
	WebDriver driver = new PhantomJSDriver();
	// open Google
	driver.get("https://www.google.ca");
	// Find the text input element by its name
	WebElement element = driver.findElement(By.name("q"));
	// Enter something to search for
	element.sendKeys("Cheese!");
	// Submit the form.
	element.submit();
	// Check the title of the page
	System.out.println("Page title is: " + driver.getTitle());
	// Wait for the page to load, timeout after 10 seconds
	(new WebDriverWait(driver, 10)).until(new ExpectedCondition<Boolean>() {
		public Boolean apply(WebDriver d) {
			return d.getTitle().toLowerCase().startsWith("cheese!");
		}
	});
	// Should see: "cheese! - Google Search"
	System.out.println("Page title is: " + driver.getTitle());
	// Close the browser
	driver.quit();

##### Install phantomjs in C# #####
Install phantomjs via NuGet Manager

![](/images/20151104-nuget-selenium-phantomjs.png)

##### apply phantomjs #####
    // initialize a WebDriver instance
    IWebDriver driver = new PhantomJSDriver();
    // load google search page
    driver.Navigate().GoToUrl("https://www.google.ca");
    // print title
    Console.WriteLine("Page title: " + driver.Title);
    // enter search word and submit
    IWebElement element = driver.FindElement(By.Name("q"));
    element.SendKeys("Cheese");
    element.Submit();
    // print title
    Console.WriteLine("Page title: " + driver.Title);
    // quit the driver
    driver.Quit();

