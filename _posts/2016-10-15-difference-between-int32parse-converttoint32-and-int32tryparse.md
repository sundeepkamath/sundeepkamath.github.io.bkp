---
layout: post
title:  "Difference between Int32.Parse, Convert.ToInt32 and Int32.TryParse"
date:   2016-10-15 14:37:00 +0530
categories: 
- C#
tags:
- C#
- integer parsing
---

In C#, we have different options to parse integer values. Amongst these, the `Parse`, `TryParse` and `Convert` options are the most common and have subtle differences in their usage.

I'll discuss each of these with a short console program.

#### Int32.Parse(string)
This converts the string representation of a number to its 32 bit signed integer equivalent.

* When `string` is `null` -> throws `ArgumentNullException`
* When `string` has a non integer value -> throws `FormatException`
* When `string` represents a number less than `MinValue` or greater than `MaxValue` -> throws `OverflowException`

Example
{% highlight C# %}
namespace ConsoleApplication
{
    public class Program
    {
        public static void Main(string[] args)
        {
            string str1 = "5555";
            string str2 = null;
            string str3 = "5555.55";
            string str4 = "51515815155151515511511515151515"; 
            int result;

            result = int.Parse(str1); //success - 5555

            result = int.Parse(str2); // ArgumentNullException    

            result = int.Parse(str3); //FormatException    

            result = int.Parse(str4); //OverflowException 
        }
    }
}

{% endhighlight %}

#### Convert.ToInt32(string)
This too converts the string representation of a number to its 32 bit signed integer equivalent.
This internally calls the Int32.Parse() method.

* When `string` is `null` -> returns 0
* When `string` has a non integer value -> throws `FormatException`
* When `string` represents a number less than `MinValue` or greater than `MaxValue` -> throws `OverflowException`

Example

{% highlight C# %}
using System;

namespace ConsoleApplication
{
    public class Program
    {
        public static void Main(string[] args)
        {
            string str1 = "5555";
            string str2 = null;
            string str3 = "5555.55";
            string str4 = "51515815155151515511511515151515"; 
            int result;

            result = Convert.ToInt32(str1); //success - 5555

            result = Convert.ToInt32(str2); // 0    

            result = Convert.ToInt32(str3); //FormatException    

            result = Convert.ToInt32(str4); //OverflowException 
        }
    }
}
{% endhighlight %}

#### Int32.TryParse(string, out int)
This too converts the string representation of a number to its 32 bit signed integer equivalent.
* If integer parsed successfully -> returns `true` and sets the integer in the `out` parameter.  
* If integer parsing is unsuccessful -> returns `false` and sets the integer in the `out` parameter to 0.  

* When `string` is `null` -> `out` variable has 0
* When `string` has a non integer value -> `out` variable has 0
* When `string` represents a number less than `MinValue` or greater than `MaxValue` -> `out` variable has 0

Example

{% highlight C# %}
using System;

namespace ConsoleApplication
{
    public class Program
    {
        public static void Main(string[] args)
        {
            string str1 = "5555";
            string str2 = null;
            string str3 = "5555.55";
            string str4 = "51515815155151515511511515151515"; 
            int result;
            bool success;

            success = Int32.TryParse(str1, out result); //true - 5555

            success = Int32.TryParse(str2, out result); //false - 0    

            success = Int32.TryParse(str3, out result); //false - 0    

            success = Int32.TryParse(str4, out result); //false - 0 
        }
    }
}
{% endhighlight %}

Usually the order of preference amongst these is ...

`Int32.TryParse` > `Convert.ToInt32` > `Int32.Parse`

Hope this was useful!
