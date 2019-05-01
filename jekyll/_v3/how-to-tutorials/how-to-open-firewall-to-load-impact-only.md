---
layout: classic-docs
title: How to open your firewall to LoadImpact only
description: Methods to open your firewall to LoadImpact traffic for testing purposes
categories: [how-to-tutorials]
order: 6
redirect_from: /knowledgebase/articles/377327-how-can-i-open-my-firewall-to-load-impact-traffic
---

***


To open your firewall to LoadImpact traffic, you have multiple options. Depending on your business needs, one may be a better fit than another.


1. Open up your firewall to the whole range of AWS IP addresses used by the load zones where you want to run your load test from. To use AWS to generate traffic, you will have to open up your firewall to a large IP range.

2. Use HTTP headers, URL query parameters, or unique data that identifies the traffic as belonging to your load test, This requires that your firewall has support for scanning application payload data and apply rules based on what it finds.

3. Add your firewall rules immediately after starting the test, when you know what IP addresses the test is using.
Here is a more samples on how to complete the different options above:

As we allocate load generator instances dynamically from cloud providers such as Amazon AWS, we don't know what source IP addresses the load generators in your test will be using.

Here is a more detailed explanation of how to do the steps:

### Opening up your firewall to all IPs potentially used in the test

Please refer to [this article]({{ site.baseurl }}/3.0/frequently-asked-questions/what-ip-addresses-are-used-by-load-impact/). You will need to whitelist the IP ranges for the load zones you are utilizing in your test

### Using HTTP headers or URL query parameters to send an identifying token

You can add custom HTTP headers to any request in your user scenario code like this:
{% highlight lua linenos %}
http.request_batch({
  {"GET", "http://test.loadimpact.com/", headers={ ["X-Myheader"]="TOKEN_STRING"} }
}
{% endhighlight %}
You'll need to add the "headers=" parameter to every single request though.

If you're not dependent on having the simulated users in your load test pretend to be a certain user agent, you can also use the `http.set_user_agent_string()` function to set the `User-Agent` header for all subsequent requests. That header could then contain your token value and you would not have to modify every single HTTP request in your user scenario.

You might also use query parameters, if it doesn't interfere with the functionality of your application:
{% highlight lua linenos %}
http.request_batch({
  {"GET", "http://test.loadimpact.com/?firewall_token=TOKEN_STRING" }
}
{% endhighlight %}
Another option could be to request content from a certain hostname that is not in the DNS, but your site would of course need to be configured to respond to requests for that hostname. This is how you do it on the LoadImpact side:
{% highlight lua linenos %}
-- Map a secret hostname to the IP of our server (need to do this if the hostname is not in the public DNS)
util.dns_remap('very_difficult.to.guess.hostname.com', '44.55.66.77')
-- Make all your requests go to this hostname
http.request_batch({
  {"GET", "https://very_difficult.to.guess.hostname.com" }
}
{% endhighlight %}
Of course, this last solution requires that your firewall terminates SSL traffic, otherwise it will not see the Host header in unencrypted form. You could also use unencrypted HTTP, but get a bit less security.

### Adding firewall rules after the start of the test

This means you start the test and then find out what IPs the load generators are using, then quickly set up firewall rules that allows incoming traffic from those IPs. To facilitate this method, you might want to design your user scenarios so they do something like this:

{% highlight lua linenos %}

if client.get_id() == 1 then
  log.info('Load generator IP address: ' .. client.get_source_ip())
end
client.sleep(300)

--rest of user scenario
{% endhighlight %}

This would cause each load generator to have client/VU thread #1 print out the IP address of the load generator in the "log" window on your test results page. All threads would wait 300 seconds (5 mins) before starting execution, giving you 5 minutes to update your firewall rules to let the traffic through from all IP addresses used by the generators.

Note that each client will in this case begin by sleeping 300 seconds, so the test rampup would be delayed by that long. The "Clients active" graph would be a bit misleading as it would also show clients that are still executing that initial sleep.
