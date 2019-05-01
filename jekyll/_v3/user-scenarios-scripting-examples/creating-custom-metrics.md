---
layout: classic-docs
title: Creating custom metrics
description: Custom metrics are used to extend the functionality of results with LoadImpact. Custom metrics can be any numeric value and are able to be plotted in the main graph.
categories: [user-scenario-scripting-examples]
order: 10
redirect_from: /knowledgebase/articles/174242-how-do-i-create-new-result-metrics
---

***

LoadImpact collects a lot of different result metrics by default, but sometimes you might want to measure something specific that we don’t currently include in the standard suite of metrics. This is where our custom metrics functionality comes in handy.

Custom metrics are very easy to use. Basically, you just calculate a sample point for your metric, then you call the [result.custom_metric()](https://loadimpact.com/load-script-api#result-custom_metric) function to store and aggregate the sample. You can store as many samples as you like, but if more than one sample is stored in the same 3-second period, an average value will be computed and stored instead of the two (or more) individual values.

#### Example:
{% highlight lua linenos %}
-- Measure the time it takes to complete several HTTP requests,
-- and then store that time as the result metric "total_load_time"

start_time = util.time()
response = http.request_batch({
    {"GET", "http://test.loadimpact.com"},
    {"GET", "http://test.loadimpact.com/image1.jpg" },
    {"POST", "http://test.loadimpact.com/login_ajax.php", data="login=test&password=test"}
})
end_time = util.time()
result.custom_metric("Total load time", end_time - start_time)

-- "http.response" can be used in this case instead of "util.time"
-- result.custom_metric("Total load time", response[1].total_load_time)
{% endhighlight %}
Custom metrics are commonly used to report:

- conditions (true/false values)
- error
- data counters
- specific behaviour of your load tests

#### Example:
{% highlight lua linenos %}
if response[1].status_code == 401 then
  result.custom_metric("PaymentError", 1)
else
  local data = json.parse(response[1].body)
  result.custom_metric("PaymentStatus", data['payment']['status'])
end
{% endhighlight %}
All metrics that you collect yourself in this way will of course be available on the test report page, allowing you to plot graphs of your metrics etc. just as with the built-in metrics.
