---
layout: classic-docs
title: What are VUs (Virtual Users)?
description: Definition of LoadImpact's Virtual Users. Complex simulated users capable of making multiple concurrent network connections.
categories: [test-configuration]
order: 2
---

***

**Simply**: Virtual users (VUs) are concurrent users that are able to open multiple connections in parallel during a test. Virtual Users constantly iterate through their user scenario until the test is over. A small number of Virtual Users can create a number of sessions magnitudes greater than their total.

Because Virtual Users are using multiple parallel network connections, they will be opening multiple concurrent network connections and transferring resources in parallel. This results in faster page loads, more stress on the target server, and more realistic result data set. **Not all load testing tools operate in this more complex and realistic fashion**

You also have access to change both the connection rules(`http.set_max_connections(max_connections, max_connections_per_host)`) and user agent strings (`http.set_user_agent_string("My UA String")`) through our Load Script API. This allows you to fine tune how the Virtual Users will operate and put stress on your system that mimics real users on a per user scenario basis. We also have some selections you can make for the entire test.


Calculating the number of virtual users can be done by using this formula:

` VUs = (hourly sessions*average session duration in seconds)/3600`

We wrote this [blog post](http://blog.loadimpact.com/blog/monthly-visits-concurrent-users/) that uses Google Analytics as an example source of this information.

***

See also:
- [Requests per second, RPS]({{ site.baseurl }}/3.0/test-configuration/what-are-requests-per-second-rps/)
- [User scenario scripting]({{ site.baseurl }}/3.0/user-scenario-scripting-examples/)
