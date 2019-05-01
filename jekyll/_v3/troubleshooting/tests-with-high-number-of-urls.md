---
layout: classic-docs
title: Running tests with large numbers of unique URLs
description: Ways to handle tests that request a large number of unique URLs.
categories: [troubleshooting]
order: 4
redirect_from: /knowledgebase/articles/762222-running-a-test-that-requests-a-large-amount-of-url
---

***

There are testing scenarios in which your VUs end up reporting a large number of unique URLs. When more than 500+ URLs have been reported during a test run or test duration is greater than 20 minutes, the test result view behaves slightly different during these tests.

- Loads data lazily depends on user pages viewing
- Turns off re-sorting tables
- Turns off time interval filtering

A common reason why such a large number of URLs are reported is that the LoadImpact platform identifies and treats every unique URL as a different URL. For example, the same URL with different query string parameters reports as two URLs:


`https://sso.login.com/login?jsessionid=ABC`

`https://sso.login.com/login?jsessionid=DEF`


Also, if your site makes a lot of requests to various tracking or cross marketing services (Google Analytics, Twitter analytics, Omniture, Chartbeat, Hubspot, Pardot, etc.), these requests often redirect to unique URLs and will quickly lead to many URLs being reported.

If you don't necessarily care about those URLs, what you should do is **remove** them from the user scenario. In some cases you will also be in violation of the service's terms of service if you run a load test against them. This is reason enough for removing these requests.

But, in the case that you need the requests and want to report results for the URLs you have two options. Use [URL grouping]({{ site.baseurl }}/3.0/test-configuration/url-grouping/) to group similar URLs together, or:
turn off the default reporting of the URL by setting `report_results=false`.
create a custom metric that will group the results of similar requests instead.
{% highlight lua linenos %}
-- Turn off normal URL reporting by setting report_results=false
local responses = http.request_batch({
{ "GET", "http://example.com/search?q="..tostring(math.random()), report_results=false }
})

-- Use a custom metric instead to group similar results
result.custom_metric("Search query", responses[1].total_load_time)
 {% endhighlight %}
