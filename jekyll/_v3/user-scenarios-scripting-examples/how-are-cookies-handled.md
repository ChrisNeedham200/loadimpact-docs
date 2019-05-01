---
layout: classic-docs
title: How are cookies handled?
description: LoadImpact handles cookies automatically, however, you have access to set them yourself and get them from the response for use in your load test.
categories: [user-scenario-scripting-examples]
order: 11
redirect_from: /knowledgebase/articles/174227-how-can-i-set-cookies-in-load-impact
---

***

While a client runs its load script, cookies set by the server (via the “Set-Cookie:” header) will be reused by the client in subsequent requests. This happens all up until the load script execution ends – then all cookies will be deleted before the next execution of the script. However, cookies, or more specifically the “Cookie:” header, is currently the only header that is set automatically by the client. Other headers, such as the “If-Modified-Since:” header will not be set unless the user specifies it in the load script, and this is why caching is not emulated automatically.

It is also possible to further set other cookies manually. You can do this by adding this to your script:

{% highlight lua linenos %}
http.request_batch({
    {"GET", "http://example.com/", headers={["Cookie"]="name=value"}}
})
{% endhighlight %}

You may also desire to get the a specific cookie from the response. Here is a code sample where we get a cookie named `JSESSIONID` from a `http.request_batch()`:

{% highlight lua linenos %}
local responses = http.request_batch({
   {"GET", "http://your_website.com/",response_body_bytes=100000},
})

local jsessid = responses[1]['cookies']['JSESSIONID']
{% endhighlight %}
