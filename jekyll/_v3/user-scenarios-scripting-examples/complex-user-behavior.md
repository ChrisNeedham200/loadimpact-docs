---
layout: classic-docs
title: Complex User Behavior
description: Lua code samples and methods for emulating complex user behavior in your Load Test
categories: [user-scenario-scripting-examples]
order: 8
redirect_from: /knowledgebase/articles/835731-simulating-complex-user-behavior
---

***

#### Simulating user login

To load test certain pages on your website you may require to create a session for the user's account on that site.

{% highlight lua linenos %}
 -- Authorize user
 local response = http.request_batch({
     {"POST", "http://test.loadimpact.com/login.php", data="login=test_user&password=1234"}
 })
 -- Get User ID and Session ID from cookies
 local uid, sid = response[1].cookies["uid"], response[1].cookies["sid"]
 if uid  "bad" or sid  "bad" then
     log.error("authorization failed")
     return -- Login failed, nothing to do
 end
 -- Get user's message list
 response = http.request_batch({
     {"GET", "http://test.loadimpact.com/my_messages.php"}
 })
 {% endhighlight %}
#### Randomize user behaviour

If you do not want to write a complex scenario, you may be able to approximate it by picking URLs randomly.
{% highlight lua linenos %}
 -- List URLs we want to randomly load
 local urls =
 {
     "http://test.loadimpact.com";
     "http://test.loadimpact.com/news.php";
     "http://test.loadimpact.com/contacts.php";
 }
 -- Get a random URL from the list - note that the Lua length operator is #
 local random_url = urls[math.random(#urls)]
 http.request_batch({
     {"GET", random_url}
 })
 {% endhighlight %}
#### Weights

If you know that your average user visits some pages more often than another, you may use that knowledge to improve the load profile approximation. The simplest way to do this is to mention the same URL several times in the list:
{% highlight lua linenos %}
 -- URLs we want to randomly load
 local INDEX = "http://test.loadimpact.com"
 local NEWS = "http://test.loadimpact.com/news.php"
 local CONTACTS = "http://test.loadimpact.com/contacts.php"
 -- Index page is seven times more popular than contacts.
 -- News page is three times more popular than contacts.
 local urls =
 {
     INDEX, INDEX, INDEX, INDEX, INDEX, INDEX, INDEX, -- x7
     NEWS, NEWS, NEWS, -- x3
     CONTACTS -- x1
 }
 -- Get a random URL from the list
 http.request_batch({
     {"GET", urls[math.random(#urls)]}
 })
 {% endhighlight %}

#### Behavior tables

To get even more flexibility, instead of using a table of URL strings, you may use a table of Lua functions, where each function would describe the behavior of one “kind” of your website’s clients. It's often easier to use multiple user scenarios, but this could be useful if you find yourself reusing similar requests and scripting methods.

You may code whole user stories this way:
{% highlight lua linenos %}
 -- URLs we want to load
 local INDEX = "http://test.loadimpact.com"
 local NEWS = "http://test.loadimpact.com/news.php"
 local CONTACTS = "http://test.loadimpact.com/contacts.php"
 -- This client gets index and then closes the browser
 local get_index = function()
     http.request_batch({
         {"GET", INDEX}
     })
 end
 -- This one gets index and then goes to read the news
 local get_index_then_news = function()
     http.request_batch({
         {"GET", INDEX}
     })
     -- Sleep a bit
     client.sleep(math.random(1, 15))
     -- Go read news
     http.request_batch({
         {"GET", NEWS}
     })
 end
 -- This one refreshes the news page few times
 -- because he can't wait for the new product announcement,
 -- and then goes to contacts to find sales' phone number.
 local refresher = function()
   local number_of_refreshes = math.random(1, 10)
   -- Come on! I can't wait for the new iThing announcement!
   for i = 1, number_of_refreshes do
       -- Is it ready?!
       http.request_batch({
          {"GET", news}
       })
       -- Sleep a bit
       client.sleep(math.random(1, 5))
   end
   -- Sleep more
   client.sleep(math.random(5, 15))
   -- I should phone them!
   http.request_batch({
       {"GET", CONTACTS}
   })
 end
 -- List of our scenarios (scenario-functions)
 local scenarios =
 {
     get_index,
     get_index_then_news,
     refresher,
 }
 -- Pick random scenarios function...
 local scenario = scenarios[math.random(#scenarios)]
 -- ...and execute it
 scenario()
 {% endhighlight %}

Refer to:

- [The LoadImpact load script API](https://loadimpact.com/load-script-api)
