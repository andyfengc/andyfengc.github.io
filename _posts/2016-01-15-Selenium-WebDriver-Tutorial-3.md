---
layout: post
title: Grab and parse data using Selenium WebDriver Part 3 - create parsing application using C#
author: Andy Feng
---

This article introduces how to use Selenium WebDriver to simulate browser operations such as load a page, manipulate DOM, extract data and provides ready-to-go sample code in Java and C#. 

## Introduction ##
This is Part 3 of Selenium tutorial.

As a test automation tool, Selenium Webdriver performs very well in simulating browser behaviour. For example, open a browser window, navigate to a specified url, click an element, executing Javascript and fetch data. Due to the unique feature to interpret javascript, Selenium Webdriver can grab some dynamic data exactly the same way that human being click an element or enter some text in a control. It is almost impossible for a search engine spider because it simply crawls links.

In Part 1 and Part 2, two fundamental demos are demonstrated. In the first demo, we load and parse a simple web page. In the second demo, we simulate search operation inside browser via Chrome driver and IE driver by explicitly opening a browser window. We even implement search funtionality via no browser webkit - PhantomJS to implement search. 

In this part, based the previous knowledge, we are ready to Parse and extract Html data using Selenium Webdriver. Let's implement an application to load tracking logs from a carrier via tracking number.

## Steps ##

## Install libraries ##
Install selenium webdriver
open nuget project manager, 

search "selenium webdriver" and install, it includes the fundamental classes
![](/images/20151104-nuget-selenium.png)

search "selenium support" and install, it provides utility classes such as WebDriverWait
![](/images/20151202-install-selenium-support.png)

search "phantomjs" and install
![](/images/20151104-nuget-selenium-phantomjs.png)

## Simulate online search ##
In order to make query and extract information, we need to load the search page first. Here is the coding. Please see part1, part2 for more details.

    private void Search(string trackingNumber)
    {
        // load page
        driver.Navigate().GoToUrl(Host);
        // wait the page loading
        driver.Manage().Timeouts().ImplicitlyWait(TimeSpan.FromSeconds(Wait_Seconds));
        // enter tracking number and search
        var element = driver.FindElement(By.TagName("input"));
        element.SendKeys(trackingNumber);
        element.Submit();
    }

## Parse data ##
The data is inside a Html table. Please see below for the table demo and Html source code.

![](/images/20151202-tracking-result.png)

![](/images/20151202-tracking-result2.png)

Now, we just need to locate the table and save each row as a log object.

	// locate the tracking log table
	var trackingHistory = driver.FindElement(By.CssSelector(".ServicesResults .textTracing tbody"));
	var rows = trackingHistory.FindElements(By.TagName("tr")).ToList();
	// extract data
	String date = null;
	String time = null;
	String status = null;
	String location = null;
	foreach (var row in rows.Skip(2))
	{
	    // parse each column
	    var cols = row.FindElements(By.TagName("td"));
	    if (cols.Count != 4)
	    {
	        throw new InvalidOperationException("the website modify the format of result");
	    }
	    if (cols[0].Text != "&nbsp" && cols[0].Text != "&nbsp;")
	        date = cols[0].Text;
	    time = cols[1].Text;
	    if (time.Length > 5)
	    {
	        time = time.Substring(0, 5);
	    }
	    status = cols[2].Text;
	    location = cols[3].Text;

		// create log objects to save datetime, status, location
		...
	}
