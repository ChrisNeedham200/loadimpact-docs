---
layout: classic-docs
title: Reading and setting HTTP headers
description: How to Set and read HTTP headers in LoadImpact
categories: [user-scenario-scripting-examples]
order: 4
redirect_from: /knowledgebase/articles/835719-http-headers
---

***

#### Reading HTTP headers from a response

You have a direct access to the HTTP headers in the response from the server. For example, we can read a header from a request and log it. In this example we only log the information for the VU with ID == 1 and on the first repetition:

{% highlight lua linenos %}
 local response = http.request_batch({
     {"GET", "http://google.com"},
 })
 if client.get_id()==1 and client.get_repetition()==1 then
   log.info(
     "google.com uses `" .. tostring(response[1].headers["Server"][1]) .. "'
     as a server software"
   )
 end
 {% endhighlight %}

#### Setting headers for a request
Sometimes you need to set custom HTTP headers for your request. One reason is to see if your server handles client-side caching.

If the web browser already has the requested resource in its cache, it may send a If-Modified-Since header, telling the server that it only wants the server to return the resource if it has been changed since last time the browser fetched it. When server sees this header, it may respond “304 Not modified”, instead of returning the actual content, thus saving bandwidth.

(Note that some web servers use an exact match for the modification date check instead of a “less-than” operation (this is explicitly allowed by the standard). So you have to send the exact date you received in the “Last-Modified” header from the server last time you requested the resource)

In the example we assume that you do know the exact page modification date.

{% highlight lua linenos %}
 local response = http.request_batch({
    {"GET", "http://test.loadimpact.com/",
        headers = {
            ["If-Modified-Since"] = "Tue, 27 Apr 2010 03:30:00 GMT";
        }
    }
 })
 if response[1].status_code==304 then
     log.info("not modified")
 elseif response[1].status_code==200 then
     log.info("modified")
 else
     log.error("unexpected code" .. tostring(response[1].status_code))
 end
 {% endhighlight %}

(Note that you may want to additionally test the support for “If-None-Match” header)

See also:

- [The LoadImpact load script API](https://loadimpact.com/load-script-api)
- [If-Modified-Since header](http://en.wikipedia.org/wiki/List_of_HTTP_headers)
