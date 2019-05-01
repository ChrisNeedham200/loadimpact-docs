---
layout: classic-docs
title: Do Virtual Users cache resources?
description: Explanation of how LoadImpact's virtual users handle caching during a test.
categories: [frequently-asked-questions]
order: 4
redirect_from: /knowledgebase/articles/174207-do-the-simulated-clients-in-a-test-cache-objects-t
---

***

No, the simulated clients in a test will never cache anything (except for cookies, which is a special case and described below). This means that in a test, every client that loads a page from your site will behave like a new visitor to the site and thus be quite “heavy” on the server.

Note, though, that because most major browsers have very low default settings for the maximum size of the browser cache, more users than you might think will be hitting your site with an empty cache – see this blog post for more info.

#### So how does client-side caching work?

Normally, what happens is that a client (browser) loads a certain resource from the server – say a .jpg image – that it needs in order to render a certain web page. A while later, the user opens another page where the same image is used. The client realizes it has the image cached on the user’s hard drive, but it needs to make sure the image hasn’t been updated on the server since last time it was fetched, so the client issues a normal request for the same image file again, but adds a special header – the If-Modified-Since: header, that tells the server that it only needs to send the resource (the image) back to the client if the image has been updated since the last time the client fetched it.

#### And how does caching work with LoadImpact?

When a LoadImpact load test is executed, it will start a certain number of simulated clients/users that access your site simultaneously. Usually, the number of clients is increased (ramped up) over time, thus increasing the load on the server. Every simulated client will execute the load script that is associated with the particular user scenario the client is running. When the client has finished executing its load script, it will execute it again, and again, and again – an infinite number of times until the load test is finished.

While a client runs its load script, cookies set by the server (via the “Set-Cookie:” header) will be reused by the client in subsequent requests. All up until the load script execution ends – then all cookies will be deleted before the next execution of the script. However, cookies, or more specifically the “Cookie:” header, is currently the only header that is set automatically by the client. Other headers, such as the “If-Modified-Since:” header will not be set unless the user specifies it in the load script, and this is why caching is not emulated automatically.

#### So how can you emulate client-side caching?

Currently, the only good way to do it is to edit your load script and specifically make your requests use the “If-Modified-Since:” header. Another option would be to remove some requests completely from the load script (i.e. the second time a certain .jpg file is loaded in the same load script you can just remove that request from the script) but this would mean the server gets fewer requests than it would normally get. The server would then never get the request that includes the “If-Modified-Since:” header. While the server would likely not send anything back to the client if/when it gets a request with that header set, every request still generates a little bit of work for the server, so removing the requests completely is not optimal (though it might be an acceptable workaround in many cases).
