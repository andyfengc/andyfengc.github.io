---
layout: post
title: Timezone conversion in programming
author: Andy Feng
categories: [timezone, programming]
---

## Introduction ##
Conversion between different timezones is a typical issue. 

For instance, I encourted an issue which grabbed datetimes with either EST (Eastern Standard Time), or AST (Alaskan Standard Time), or PST (Pacific Standard Time) from service provider. However, our local database is designed in EST timezone. Therefore, we have to identify the right timezone and make conversion accordingly.

The requirement is:

1. identify the right source timezone by abbreviated form e.g. PST, AST...
2. determine the destination timezone - EST
3. convert the souce time to target time
	
	in C#, the code is as easy as one line of code
		
		var targetTimeZoneInfo = TimeZoneInfo.FindSystemTimeZoneById("Pacific Standard Time");
		var targetTime = TimeZoneInfo.ConvertTime(sourceTime, targetTimeZoneInfo);

The question is, how to constructor the targetTimeZoneInfo object based on abbreviation forms of timezone.

## Research ##
There are tons of lists of timezones over Internet. 

such as wikipedia:

[List of time zone abbreviations](https://en.wikipedia.org/wiki/List_of_time_zone_abbreviations)

![](/images/posts/20171003-timezone-1.png)

or [Time Zone Abbreviations – Worldwide List](https://www.timeanddate.com/time/zones/)

### Problem ###

Please note that there is no one-to-one correspondence between abbreviations and time zones. More exactly, more than one time zone can be mapped onto the same abbreviated form.

For example, "AMT" means UTC+4 (Armenia Time) and UTC-4 (Amazon Time) at the same time. Same thing with "EDT" and other abbreviations. 

![](/images/posts/20171003-timezone-3.png)

On other words, abbreviated forms are useless for identification purposes, so their use should be best avoided as they can cause confusion. Even if you use abbreviated form of the name, it should be accompanied by UTC notation to avoid ambiguity.

Because of this reason, .NET does not support abbreviated forms at all. We can list all available timezone information via `TimeZoneInfo.GetSystemTimeZones()`

![](/images/posts/20171003-timezone-2.png)

Here is the complete list of timezone in C#. [Time Zone IDs](https://msdn.microsoft.com/en-us/library/gg154758.aspx)

![](/images/posts/20171003-timezone-4.png)

As we see, no standard abbreviations of timezone included!

## C# Solution ##
The current solution is, create customized converter to translate abbreviated form to full name of timezone. Then use the previous code to convert the time.

Even though the abbreviated forms are not unique for global timezone, they are unique for Canada and US.

[Canada Time Zone Abbreviations](http://www.timetemperature.com/abbreviations/canada_time_zone_abbreviations.shtml)

[United States Time Zone Abbreviations](http://www.timetemperature.com/abbreviations/united_states_time_zone_abbreviations.shtml)

Create a timezone utility

    public enum TimeZoneEnum
    {
        // canada
        NST,
        NDT,
        AST,
        ADT,
        EST,
        EDT,
        CST,
        CDT,
        MST,
        MDT,
        PST,
        PDT,
        YST,
        YDT,
        // us
        AKST,
        AKDT,
        HST,
        HAST,
        HADT,
        SST,
        SDT,
        CHST
    }

    public static class TimeZoneEnumExtensions
    {
        public static TimeZoneEnum ToCode(string timezoneStr)
        {
            switch (timezoneStr.ToUpper())
            {
                // ca
                case "NST":
                    return TimeZoneEnum.NST;
                case "NDT":
                    return TimeZoneEnum.NDT;
                case "AST":
                    return TimeZoneEnum.AST;
                case "ADT":
                    return TimeZoneEnum.ADT;
                case "EST":
                    return TimeZoneEnum.EST;
                case "EDT":
                    return TimeZoneEnum.EDT;
                case "CDT":
                    return TimeZoneEnum.CDT;
                case "CST":
                    return TimeZoneEnum.CST;
                case "MST":
                    return TimeZoneEnum.MST;
                case "MDT":
                    return TimeZoneEnum.MDT;
                case "PST":
                    return TimeZoneEnum.PST;
                case "PDT":
                    return TimeZoneEnum.PDT;
                case "YST":
                    return TimeZoneEnum.YST;
                case "YDT":
                    return TimeZoneEnum.YDT;
                // us
                case "AKST":
                    return TimeZoneEnum.AKST;
                case "AKDT":
                    return TimeZoneEnum.AKDT;
                case "HST":
                    return TimeZoneEnum.HST;
                case "HAST":
                    return TimeZoneEnum.HAST;
                case "HADT":
                    return TimeZoneEnum.HADT;
                case "SST":
                    return TimeZoneEnum.SST;
                case "SDT":
                    return TimeZoneEnum.SDT;
                case "CHST":
                    return TimeZoneEnum.CHST;
                default:
                    throw new NotSupportedException("undefined timezone string: " + timezoneStr);
            }
        }
        public static string ToCode(this TimeZoneEnum timezone)
        {
            switch (timezone)
            {
                // ca
                case TimeZoneEnum.NST:
                    return "NST";
                case TimeZoneEnum.NDT:
                    return "NDT";
                case TimeZoneEnum.AST:
                    return "AST";
                case TimeZoneEnum.ADT:
                    return "ADT";
                case TimeZoneEnum.EST:
                    return "EST";
                case TimeZoneEnum.EDT:
                    return "EDT";
                case TimeZoneEnum.CDT:
                    return "CDT";
                case TimeZoneEnum.CST:
                    return "CST";
                case TimeZoneEnum.MST:
                    return "NST";
                case TimeZoneEnum.MDT:
                    return "NST";
                case TimeZoneEnum.PST:
                    return "PST";
                case TimeZoneEnum.PDT:
                    return "PDT";
                case TimeZoneEnum.YST:
                    return "YST";
                case TimeZoneEnum.YDT:
                    return "YDT";
                // us
                case TimeZoneEnum.AKST:
                    return "AKST";
                case TimeZoneEnum.AKDT:
                    return "AKDT";
                case TimeZoneEnum.HST:
                    return "HST";
                case TimeZoneEnum.HAST:
                    return "HAST";
                case TimeZoneEnum.HADT:
                    return "HADT";
                case TimeZoneEnum.SST:
                    return "SST";
                case TimeZoneEnum.SDT:
                    return "SDT";
                case TimeZoneEnum.CHST:
                    return "CHST";
                default:
                    throw new NotSupportedException("undefined timezone: " + timezone);
            }
        }

        public static string ToName(this TimeZoneEnum timezone)
        {
            switch (timezone)
            {
                // ca
                case TimeZoneEnum.NST:
                case TimeZoneEnum.NDT:
                    return "Newfoundland Standard Time";
                case TimeZoneEnum.AST:
                case TimeZoneEnum.ADT:
                    return "Atlantic Standard Time";
                case TimeZoneEnum.EST:
                case TimeZoneEnum.EDT:
                    return "Eastern Standard Time";
                case TimeZoneEnum.CST:
                case TimeZoneEnum.CDT:
                    return "Central Standard Time";
                case TimeZoneEnum.MST:
                case TimeZoneEnum.MDT:
                    return "Mountain Standard Time";
                case TimeZoneEnum.PST:
                case TimeZoneEnum.PDT:
                    return "Pacific Standard Time";
                // us
                case TimeZoneEnum.AKST:
                case TimeZoneEnum.AKDT:
                    return "Alaskan Standard Time";
                case TimeZoneEnum.HST:
                case TimeZoneEnum.HAST:
                case TimeZoneEnum.HADT:
                    return "Hawaiian Standard Time";
                case TimeZoneEnum.SST:
                case TimeZoneEnum.SDT:
                    return "Samoa Standard Time";
                default:
                    throw new NotSupportedException("undefined timezone: " + timezone);
            }
        }
    }

Then, convert timezone like this:

	var pstTime = TimeZoneInfo.ConvertTime(time, TimeZoneInfo.Local, TimeZoneInfo.FindSystemTimeZoneById(TimeZoneEnum.PST.ToName()));

