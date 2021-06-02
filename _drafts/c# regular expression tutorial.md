---
layout: post
title: Cache Tutorial
author: Andy Feng
---

# Introduction #
C# regular expression is used for pattern matching in C# applications. Regular expressions are a pattern matching standard for string parsing and replacement and is a way for a computer user to express how a computer program should look for a specified pattern in text and then what the program is to do when each pattern match is found.

C# Regex class is used for creating a C# regular expression.

# Regular expression syntax

Special characters - Special characters in a regex are used to assign several different meanings to a pattern.

![](/images/posts/20201222-regex-1.png)

Quantifiers - Quantifier syntax is used to count or quantify the matching criteria. For example, if you want to check if a particular string contains an alphabet one or more times. 

![](/images/posts/20201222-regex-2.png)

Character sets - used to tell the regex engine to look for a single match out of several characters. 

![](/images/posts/20201222-regex-3.png)

Grouping - allows the user to either add a quantifier with the expression.

![](/images/posts/20201222-regex-4.png)

# check if match a string
`isMatch()` method indicates whether the regular expression finds a match in the input string.

e.g.

	var words = new List<string>() { "Seven", "even",
	        "Maven", "Amen", "eleven" };
	
	var regex = new Regex(@".even", RegexOptions.Compiled);
	
	foreach (string word in words)
	{
	    if (regex.IsMatch(word))
	    {
	        Console.WriteLine($"{word} does match");
	    }
	    else
	    {
	        Console.WriteLine($"{word} does not match");
	    }
	}

# Match a string
To test whether a regular expressions matches a string, you can use the static method Regex.Match() which takes an optional set of RegexOptions enums. This returns a Match object which contains information about where the match (if any) was found.

> Match match = Regex.Match(InputStr, Pattern, RegexOptions)
> 
> - RegexOptions are optional

e.g.

	// Lets use a regular expression to match a date string.
	string pattern = @"([a-zA-Z]+) (\d+)";
	
	Match result = Regex.Match("June 24", pattern);
	if (result.Success) { // Indeed, the expression "([a-zA-Z]+) (\d+)" matches the date string
	    // To get the indices of the match, you can read the Match object's
	    // Index and Length values.
	    // This will print [0, 7], since it matches at the beginning and end of the 
	    // string
	    Console.WriteLine("Match at index [{0}, {1})", 
	        result.Index,
	        result.Index + result.Length);
	
	    // To get the fully matched text, you can read the Match object's Value
	    // This will print "June 24"
	    Console.WriteLine("Match: {0}", result.Value);
	
	    // If you want to iterate over each of the matches, you can call the 
	    // Match object's NextMatch() method which will return the next Match
	    // object.
	    // This will print out each of the matches sequentially.
	    while (result.Success) {
	        Console.WriteLine("Match: {0}", result.Value);
	        result = result.NextMatch();
	    }
	}

# Match all strings
If we wanted to perform a global search over the whole input string and return all the matches with their corresponding capture data, we can instead use the static method `Regex.Matches()` to get a `MatchCollection` which can be iterated over.

> MatchCollection matches = Regex.Matches(InputStr, Pattern, RegexOptions)

e.g.

	// Lets use a regular expression to capture data from a few date strings.
	string pattern = @"([a-zA-Z]+) (\d+)";
	MatchCollection matches = Regex.Matches("June 24, August 9, Dec 12", pattern);
	
	// This will print the number of matches
	Console.WriteLine("{0} matches", matches.Count);
	
	// This will print each of the matches and the index in the input string
	// where the match was found:
	//   June 24 at index [0, 7)
	//   August 9 at index [9, 17)
	//   Dec 12 at index [19, 25)
	foreach (Match match in matches) {
	    Console.WriteLine("Match: {0} at index [{1}, {2})", 
	        match.Value, 
	        match.Index, 
	        match.Index + match.Length);
	}
	
	// For each match, we can extract the captured information by reading the 
	// captured groups.
	foreach (Match match in matches) {
	    GroupCollection data = match.Groups;
	    // This will print the number of captured groups in this match
	    Console.WriteLine("{0} groups captured in {1}", data.Count, match.Value);
	
	    // This will print the month and day of each match.  Remember that the
	    // first group is always the whole matched text, so the month starts at
	    // index 1 instead.
	    Console.WriteLine("Month: " + data[1] + ", Day: " + data[2]);
	
	    // Each Group in the collection also has an Index and Length member,
	    // which stores where in the input string that the group was found.
	    Console.WriteLine("Month found at[{0}, {1})", 
	        data[1].Index, 
	        data[1].Index + data[1].Length);
	}

# find and replace strings
use `Regex.Replace()` to find and replace a part of a string using regular expressions. The replacement string can either be a regular expression that contains references to captured groups in the pattern, or just a regular string.

`string replaced = Regex.Replace(InputStr, Pattern, ReplacementPattern, RegexOption)`

e.g.

	// Lets try and reverse the order of the day and month in a few date 
	// strings. Notice how the replacement string also contains metacharacters
	// (the back references to the captured groups) so we use a verbatim 
	// string for that as well.
	string pattern = @"([a-zA-Z]+) (\d+)";
	
	// This will reorder the string inline and print:
	//   24 of June, 9 of August, 12 of Dec
	// Remember that the first group is always the full matched text, so the 
	// month and day indices start from 1 instead of zero.
	string replacedString = Regex.Replace("June 24, August 9, Dec 12",
	    pattern, @"$2 of $1");
	Console.WriteLine(replacedString);

# Split text

# demo

	string patternText = "Hello";
	Regex reg = new Regex(patternText);
	
	//IsMatch(string input)
	Console.WriteLine(reg.IsMatch("Hello World"));
	
	//IsMatch(string input, int index)
	Console.WriteLine(reg.IsMatch("Hello", 0));
	
	//IsMatch(string input, string pattern)
	Console.WriteLine(Regex.IsMatch("Hello World", patternText));
	
	//Replace(string input, string replacement)
	Console.WriteLine(reg.Replace("Hello World", "Replace"));
	
	//Split(string input, string pattern)
	string[] arr = Regex.Split("Hello_World_Today", "_");
	foreach(string subStr in arr)
	{
	    Console.WriteLine("{0}", subStr);
	}
 
result:

	True
	True
	True
	Replace World
	Hello
	World
	Today

# FAQ
## common regular expressions
1. Validate if the input string is composed of 6 digit case-insensitive alphabet characters.

	`var pattern = @"^[a-zA-Z]{6}$"`

1. validate that a word that starts with “Super” and has white space after that

1. validate Canadian postal code

	`string pattern = @"^[ABCEGHJKLMNPRSTVXY]{1}\d{1}[A-Z]{1}[\s|-]*\d{1}[A-Z]{1}\d{1}$"`
	`var pattern = @"^Super\s"`

1. validate image file name

	`var pattern = @"(\w+)\.(jpg|png|jpeg|gif)$";`

1. validate a website
	
	`string pattern = @"^www.[a-zA-Z0-9]{3,20}.(com|in|org|co\.in|net|dev)$";`

1. validate an email

	`string pattern = @"^[a-zA-Z0-9\._-]{5,25}.@.[a-z]{2,12}.(com|org|co\.in|net)";`

1. validate a date

	`\d{4}-\d{2}-\d{2}` - 0001-01-01 to 9999-99-99

	or

	`(19|20)\d\d[-](0[1-9]|1[012])[-](0[1-9]|[12][0-9]|3[01])` - 1900-01-01 to 2099-12-31

# References #
[Using Regular Expressions in C#](https://regexone.com/references/csharp)

[C# Regex Tutorial: What Is A C# Regular Expression](https://www.softwaretestinghelp.com/csharp-regex-tutorial/)

[C# Regular Expressions tutorial](http://zetcode.com/csharp/regex/)