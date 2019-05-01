---
layout: classic-docs
title: Verifying resource contents
description: How to verify resource contents in a response, these can be used in combination with custom metrics to make your tests more meaningful or just as a way to deal with errors from performance problems
categories: [user-scenario-scripting-examples]
order: 5
redirect_from: /knowledgebase/articles/835722-verifying-resource-contents
---

***

#### Verifying resource contents
When your website gets stressed it may at some point start to return errors instead of expected page content. Sometimes these errors can be in the form of HTTP error codes (500-codes), which are easy to detect on our side. 500-errors constitute “unsuccessful” HTTP transactions where the server returned no useful content and for those transactions you get separate statistics in our user interface (you can plot separate graphs for them etc).

However, another common outcome is that you get valid HTML code back, and a 200-response (indicating a successful HTTP transaction), but where the actual content returned is just an HTML page with an error message. A human user will read the error message and note that it was not the content they were expecting, but how does a computer program (our simulated user running the load script) do that? This is where you need to write content verification code.

Here is a script that verifies that it is getting the expected content, and if it doesn’t, it will log an error message:
{% highlight lua linenos %}
 local response = http.request_batch({
     {"GET", "http://test.loadimpact.com/pi.php?decimals=18",
         response_body_bytes = 100  -- Tell the system it should store up to 100 bytes of body data for us
     }
 })
 if response[1].body ~= "3.141592653589793238" then
   log.error(
      "PI calculator returned unexpected result when stressed with "
         .. test.get_clients() .. " simulated clients: " .. response[1].body
   )
 end
 {% endhighlight %}

**IMPORTANT!** – don’t forget to add the last parameter `response_body_bytes=100`  to the GET request if you want to examine the reply from the server. Without this extra parameter, response.body will be empty. This is a very common mistake many make. It might seem like a silly parameter to require, but many server responses can be quite big, consuming a lot of memory on the load generator host if we have to save them all, and most of the time, they are not used at all by the client load script.



#### Verifying batch request content

The `http.request_batch()` returns a table of `http.Response` objects, one for each request:

{% highlight lua linenos %}
 -- Create a table with correct request sizes
 local sizes = {
     103,
     3533,
     4,
 }
 -- Request resources
 local responses = http.request_batch({
     {"GET", "http://test.loadimpact.com/style.css"},
     {"GET", "http://test.loadimpact.com/images/logo.png"},
     {"GET", "http://test.loadimpact.com/pi.php"},
 })
 -- All requested URLs are for static resources, so we verify size.
 for i = 1, #responses do
     if responses[i].body_size ~= sizes[i] then
        log.error(
            "Unexpected body size for " .. responses[i].request_url
            .. " when stressed with with " .. test.get_clients()
            .. " simulated clients: got " .. responses[i].body_size
            .. " expected " .. sizes[i]
        )
     end
 end
 {% endhighlight %}
**Note:** In this example since we are just examining the body size, we do not need to save any `response_body_bytes`. We only need to save `response_body_bytes` when we need to access the response itself.


See also:

 - [LoadImpact Load Script API (Lua)](https://loadimpact.com/load-script-api)
