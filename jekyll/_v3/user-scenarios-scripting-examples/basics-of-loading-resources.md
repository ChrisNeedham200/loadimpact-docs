---
layout: classic-docs
title: Basics to requests in LoadImpact
description: High level overview of making HTTP(s) requests with LoadImpact
categories: [user-scenario-scripting-examples]
order: 2
redirect_from: /knowledgebase/articles/835704-load-resources
---

***

#### Requesting a single resource

The simplest possible behavior for the client is to issue a HTTP request for a just one single resource (URL) on your site. Lets try it out:

{% highlight lua linenos %}
 http.request_batch({
     {"GET", "http://test.loadimpact.com/"}
 })
{% endhighlight %}

That’s it. Each client will issue a single GET request, via `http.request_batch()`, to the given URL every time it executes the load script. In the example the URL is `http://test.loadimpact.com`, you should of course replace it with an URL that accesses your site.

***

#### Using HTTPS

Note that you may use either HTTP or HTTPS when fetching resources. To use HTTPS you just change the URL so it starts with `https://`:

{% highlight lua linenos %}
 http.request_batch({
     {"GET", "https://test.loadimpact.com/"}
 })
{% endhighlight %}

***

#### GET request parameters

If you need to pass GET request parameters, just add them to the URL string:

{% highlight lua linenos %}
http.request_batch({
     {"GET", "http://test.loadimpact.com/pi.php?decimals=18"}
 })
{% endhighlight %}

 ***

#### POST requests

To pass POST data, use the `POST` method and supply the post data using the data parameter.

{% highlight lua linenos %}
http.request_batch({
     {"POST", "http://test.loadimpact.com/login.php", data="login=test_user&password=1234"}
 })
{% endhighlight %}

***

#### Load-test multiple resources

Your website visitors might commonly visit more than one page on your site. This means that load testing a single page is not going to be a very realistic load test. To create a more realistic “user scenario”, we want our load test to simulate the behavior of our site visitors, i.e. load multiple pages.

(Note to the advanced reader: we will simplify things a bit here, and pretend, that you’re only interested in loading the main HTML code for a page, without all the page dependencies)

Let’s try out a simple case first. Say, you notice that your users usually visit two URLs during a session: the news page and the page with contacts. You then have to fetch these two URLs in the client script:



{% highlight lua linenos %}
http.request_batch({
     {“GET”, “http://test.loadimpact.com/news.php”}
 })
 http.request_batch({
     {“GET”, “http://test.loadimpact.com/contacts.php”}
 })
{% endhighlight %}



Refer to:

- [The LoadImpact load script API](https://loadimpact.com/load-script-api)
- [http.post()](https://loadimpact.com/load-script-api#http-post)
- [http.get()](https://loadimpact.com/load-script-api#http-get)
- [http module](https://loadimpact.com/load-script-api#http)
- [http.request()](https://loadimpact.com/load-script-api#http-request)
- [http.request_batch()](https://loadimpact.com/load-script-api#http-request_batch)
