---
layout: classic-docs
title: Using client.sleep() to add delays between requests
description: Explanation of client.sleep() and why it's important to include in your load scripts
categories: [user-scenario-scripting-examples]
order: 3
redirect_from:
  - /knowledgebase/articles/835716-delay-between-requests
  - /knowledgebase/articles/174292-what-is-load-impact-s-default-sleep-time
---

***

It is uncommon for a visitor to load one single page and then leave your site, it is probably very uncommon that a user loads one page and then immediately loads another page, with no time to read the content on the first page. Humans need a little while to process information on a page before moving on. It is often called “think time” or “page view time”. To simulate this behavor of real, human visitors, we have to add delays to our load script:
{% highlight lua linenos %}
 http.request_batch({
     {"GET", "http://test.loadimpact.com/news.php"},
 })
 client.sleep(
    math.random(1, 15) -- Sleeps between 1 and 15 seconds before continuing
 )
 http.request_batch({
     {"GET", "http://test.loadimpact.com/news.php"},
 })
 {% endhighlight %}

You can identify and adjust sleeps in our load testing script. by default, we add a sleep time between pages and to the end of scripts recorded with the Chrome extension:

{% highlight lua linenos %}
client.sleep(math.random(20, 40)) -- Sleep between 20 to 40 seconds
{% endhighlight %}

The sleep time between your `http.page_start()` and `http.page_end()` in your script will depend on how long you spend "sleeping" in your recording. You may edit this as needed. The `math.random(x,y)` fucntion comes in handy to make this more dynamic. The below is an example of what is generated automatically. It is recommended to add the `math.random()` function to your sleep times.

{% highlight lua linenos %}
client.sleep(5)
{% endhighlight %}


See also:

- [The LoadImpact load script API](https://loadimpact.com/load-script-api)
- [Built in module: client.sleep()](https://loadimpact.com/load-script-api#client-sleep)
- [Lua standard Library: math.random() ](http://www.lua.org/manual/5.1/manual.html#pdf-math.random)
