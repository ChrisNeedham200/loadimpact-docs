---
layout: classic-docs
title: Emulating Browser Behavior
description: When running load tests it important to consider how a real user would make requests in the browser. Real browsers open multiple connections in parallel. The request_batch() allows you to emulate this behavior.
categories: [user-scenario-scripting-examples]
order: 7
redirect_from:
  - /knowledgebase/articles/835728-simulating-browser-behavior
  - /knowledgebase/articles/174622-emulating-a-specific-browser
---

***

When a browser loads a HTML page it usually requests a whole series of resources/files – it loads first the main HTMLcode, then CSS files, images, Javascript files, and so on. It can even load other HTML files in iframe tags or via AJAXcalls.

Modern browsers load all these resources asynchronuously, using multiple network connections over which they issue several requests in parallel. This gets the whole web page loaded faster, and also puts a lot of extra stress on the web server, which has to deliver a lot of different resources in a short amount of time, across several network connections. If you want your load test to be realistic, you should try and make sure that your simulated clients emulate this browser behaviour as closely as possible. **If you have used the [Chrome extension]({{ site.baseurl }}/3.0/user-scenarios/load-impact-chrome-extension/) to record your user scenario, you will notice that most requests are already in batches.**

You can use `http.request()` (or its wrappers, `http.get()` and `http.post()`) to issue a blocking HTTP request call that will load only a single resource, but if you want to take advantage of the ability of the LoadImpact load generator to load multiple resources at once, across multiple network connections, you should use `http.request_batch()` to issue multiple request in parallel.

Here is an example that shows a load script that loads all the content on the page `http://test.loadimpact.com/` in a way that emulates a modern browser:
{% highlight lua linenos %}
 http.get("http://test.loadimpact.com") -- Get the main HTML code for the page
 -- Get resources mentioned in the HTML code we just loaded
 http.request_batch({
     {"GET", "http://test.loadimpact.com/style.css" },
     {"GET", "http://test.loadimpact.com/favicon.ico" }
 })
 -- Get all the resources that are mentioned in the CSS
 http.request_batch({
     {"GET", "http://test.loadimpact.com/images/logo.png" }
 })
 {% endhighlight %}

 ***

## Emulating a Specific Browser

 To emulate a specific browser, you can use the load script API functions `http.set_user_agent_string()` and `http.set_max_connections()` to configure the simulated client to behave as if it was the browser in question.

 Here is code where we perform an emulation of IE6. We chose IE6 for illustrative purposes.
 {% highlight lua linenos %}
 -- Emulating IE6
 http.set_user_agent_string("Mozilla/5.0 (Windows; U; MSIE 6.0; Windows NT 5.1; SV1; .NET CLR 2.0.50727)")
 http.set_max_connections(34, 2)
 -- make several HTTP requests as IE6
 http.request_batch({
      {"GET", "http://test.loadimpact.com/"},
      {"GET", "http://test.loadimpact.com/index.php"},
      {"GET", "http://test.loadimpact.com/news.php"},
      {"GET", "http://test.loadimpact.com/contacts.php"}
 })
 {% endhighlight %}

 In the above example, we configure the simulated client to do two things:

 Use the specific User-Agent string used by an [Internet Explorer 6](http://www.browserscope.org/?category=network&v=1&ua=Chrome%2022%2CIE%206) browser running on Windows XP
 Use max 2 concurrent connections per target host, and max 34 concurrent connections in total in the first example, like IE6 would.


 The result will be that because all the four requests we make are to the same host (“test.loadimpact.com”), 2 connections will be used to fetch 2 things in parallel. If each transaction takes e.g. 100 ms it means the whole batch of requests will have taken a total of 200 ms to complete for IE6.

 ***

 This is an example of the same code, but instead emulating Chrome 22:
 {% highlight lua linenos %}
 -- Emulating Chrome 22
 http.set_user_agent_string("Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.1 (KHTML, like Gecko) Chrome/22.0.1207.1 Safari/537.1")
 http.set_max_connections(17, 6)
 -- make several HTTP requests as Chrome 22
 http.request_batch({
      {"GET", "http://test.loadimpact.com/"},
      {"GET", "http://test.loadimpact.com/index.php"},
      {"GET", "http://test.loadimpact.com/news.php"},
      {"GET", "http://test.loadimpact.com/contacts.php"}
 })
 {% endhighlight %}
 [Chrome 22](http://www.browserscope.org/?category=network&v=1&ua=Chrome%2022%2CIE%206) uses up to 6 concurrent connections to the same host, and up to 17 concurrent connections in total. In our example that would mean that Chrome was able to open 4 concurrent connections to the “test.loadimpact.com” host and fetch all four items in parallel. If each item takes 100 ms to fetch, it would mean the whole batch of requests were done in just 100 ms.

 The User-Agent string we set in both examples will be sent in the “User-Agent:” HTTP header to the web server when our simulated client makes a request, making the remote web server believe that the client is using this particular browser/platform combination. For some sites it can trigger delivery of specialized content that has been adapted for a particular browser and/or platform.

 Because Chrome uses up to three times as many concurrent connections as IE6 to the same host when it is fetching things, it means it can fetch things much faster (up to three times faster). This also places the target host/system under a lot more pressure, which is why it is important to emulate the right mix of browsers when you run your load test.


 See also:

 - [Function http.set_user_agent_string()](https://loadimpact.com/load-script-api#http)
 - [Function http.set_max_connections()](https://loadimpact.com/load-script-api#http)
 - [HTTP User Agent strings](http://www.useragentstring.com/)
 - [Browser network characteristics IE6 vs Chrome 22](http://www.browserscope.org/?category=network&v=1&ua=Chrome%2022%2CIE%206)
