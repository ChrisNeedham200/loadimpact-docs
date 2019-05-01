---
layout: classic-docs
title: How to test a site that requires login
description: Example on how to structure your http requests to test a site that requires login
categories: [how-to-tutorials]
order: 3
redirect_from: /knowledgebase/articles/174255-how-do-i-test-a-site-where-you-need-to-login
---

***

LoadImpact supports both basic HTTP authentication and HTTP POST operations, as well as HTTPS. This means we support most common methods for logging in to sites.

#### Login via HTTP POST

Many sites let users login by filling in their username and password in input fields of a form that is then POST:ed to the web server. LoadImpact supports HTTP POST operations, which mean that you may edit your load script and provide the login credentials (username/password) in a POST operation. When the server gets the right login credentials through this POST operation, it sets a session cookie in your browser. The session cookie is used for all future communications with the web server, authenticating you as a logged-in user. LoadImpact automatically sets cookies when asked to by the server, so accessing pages as a logged-in user works fine if you just make sure your load script performs that initial POST operation.

Refer to:

[The LoadImpact load script API – http.post](https://loadimpact.com/load-script-api#http-post)

***

#### Basic HTTP Authentication

Basic HTTP Authentication is a method where the server will only honor requests if they contain a special HTTP header (the “Authorization:” header) where the client has to place the login credentials (username and password) that are to be used to gain access to the site or page.

LoadImpact supports Basic HTTP Authentication by the use of URLs in this format:
{% highlight lua linenos %}
http://username:password@www.mydomain.com/index.php
{% endhighlight %}
If you use URLs of that format, that include the username and password to be used when accessing the site/page, requests will automatically get the “Authorization:” header, with the correct data in them.

We also allow HTTP request headers to be specified for each transaction, so it is possible to create the data for the “Authorization:” header in the load script code and then add the header to each request.
{% highlight lua linenos %}
http.request({
  method="GET",
  url="http://example.com/",
  headers={ ["Authorization"]= 'Basic ' .. base64.encode(username .. ':' .. password) }
})
{% endhighlight %}
Refer to:
- [User scenario scripting]({{ site.baseurl }}/3.0/user-scenario-scripting-examples/)
- [The LoadImpact load script API](https://loadimpact.com/load-script-api)
- [Setting HTTP headers for a request]({{ site.baseurl }}/3.0/user-scenarios-scripting-examples/http-headers/)
- [How to load test an API]({{ site.baseurl }}/3.0/how-to-tutorials/how-to-load-test-an-api/)
