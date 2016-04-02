---
layout: post
title: Converting UTC date time from server to local date time on the browser client using JavaScript
categories:
- ASP.Net
- I18N
- JavaScript
- Web   
tags:
- datetime
- Globalization
- I18N
- javascript
- local time
- moment.js
- UTC
---

There is this web application that I've been working on.  
I wanted to be able to handle globalization while displaying **datetime** on the web page.

This **datetime** was stored on the server in **UTC** form.  
I wanted it to be displayed on the client-side i.e. the browser in the **local time**.

This was an ASP.Net MVC application and I initially added the following code snippet in the Razor view to display the datetime(@Model.PostedOn contained the UTC datetime)...

{% highlight html %}
<div class="posted-date">
	@Model.PostedOn
</div>
{% endhighlight %}

The next step was to convert this to the local time on the browser client.  
This could be done using the `getTimezoneOffset()` method that javascript provides as follows...


{% highlight javascript %}
$(document).ready(function () {
    // Determine timezone offset in milliseconds
    // from: http://www.w3schools.com/jsref/jsref_getTimezoneOffset.asp
   
    var date = new Date()
    var offsetms = date.getTimezoneOffset() * 60 * 1000;
    $('.posted-date').each(function () {
        try {
            var text = $(this).html();

            var serverDate = new Date(text);
            serverDate = new Date(serverDate.valueOf() - offsetms);

            $(this).html(serverDate.toDateString() + " " + serverDate.toLocaleTimeString());
        }
        catch (ex) {
            console.warn("Error converting time", ex);
        }
    });
});
{% endhighlight %}  

While this could be one way to achieve it, I found that the third party library [moment.js] provided a **much more cleaner** way to approach this.  
All I had to do was to add reference to the **moment.min.js** library and the above code could now be refactored as...

{% highlight javascript %}
$(document).ready(function () {
    $('.posted-date').each(function () {
        try {
                var text = $(this).html();
                var serverDate = moment.utc(text).local().format('LLL'); 

                $(this).html(serverDate);
        }
        catch (ex) {
            console.warn("Error converting date time", ex);
        }
    });
});
{% endhighlight %}

It helps achieve this in a single line of code and additionally also provides various parameters to handle the **locale formats**.

{% highlight javascript %}
moment().format('L');    // 08/25/2015
moment().format('l');    // 8/25/2015
moment().format('LL');   // August 25, 2015
moment().format('ll');   // Aug 25, 2015
moment().format('LLL');  // August 25, 2015 1:02 AM
moment().format('lll');  // Aug 25, 2015 1:02 AM
moment().format('LLLL'); // Tuesday, August 25, 2015 1:02 AM
moment().format('llll');  // Tue, Aug 25, 2015 1:02 AM
{% endhighlight %}

Let me know which approach you find convenient and the libraries you usually use for this purpose.  
  
[moment.js]: http://momentjs.com/
 