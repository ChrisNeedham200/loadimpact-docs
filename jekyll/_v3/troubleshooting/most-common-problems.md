---
layout: classic-docs
title: Most common problems
description: The most common problems you may run into when running load tests.
categories: [troubleshooting]
order: 1
redirect_from: /knowledgebase/articles/173855-common-problems-when-running-load-tests
---

***

When running a test, a lot of different things can happen. The following is a list of some of the most common problems that you might face when running a load test, and how to deal with it.

- TOC
{:toc}


### Tests aborted by system

This issue has a variety of causes, typically on the scripting and configuration side. We've written an article specifically on this topic and the most common (and uncommon) reason for this to happen:  [Tests aborted by system]({{ site.baseurl }}/3.0/troubleshooting/tests-aborted-by-system/)


***


### Sharp increase in VU load time

When running a test and the VU load time increases by a huge amount (e.g. tenfold) very quickly, before the test has gotten very far, there is usually no point in keeping the test going. In most cases, it means the load levels you are trying to use in the test far exceeds the capacity of the target system. If the target system seems unable to handle even a fraction of the load you expected it to handle, it might be that your User scenarios are too aggressive. You should then double-check that your User scenarios accurately emulates the (expected) behavior of real users on the site – that they spend enough time sleeping to simulate user think time (page view time), etc. Continuing the test in such a situation usually means you will just waste Credits and not get any further useful information out of the test. If the test is aborted early on, a partial refund of the Credits used will be issued.

***

### IP address is blacklisted
This can happen for one of two different reasons:

##### You're trying to test a private IP address

If you see that the system tries to connect to an address beginning with "10." (for example "10.121.44.32") or "192.168." (for example: "192.168.0.1") or "172.16." the test will not start because these address ranges are [private address space](http://en.wikipedia.org/wiki/Private_network), not reachable across the public Internet. If you have entered the IP addresses yourself when creating your load test configuration it means you have some homework to do, finding out what public IP addresses the site/app that you are trying to test is using. A private address is likely used internally, on a corporate LAN, while a public address is necessary for other Internet hosts (in this case, the LoadImpact load generators) to be able to reach your system.

If you did not enter the address/es yourself into your load test configuration, but instead wrote the name of your site (e.g. "www.acme.com") and still get this problem and LoadImpact complaining that you're trying to test a private IP address, then it means that the name of your site (e.g. "www.acme.com") is being resolved to a private IP address by the DNS system.

If this was not intentionally done by your DNS administrator there is another common way this happens, and that is when you are hosted on Amazon AWS, is using Amazon's DNS services for your domain, and are running the load test from Amazon-based load generators. What happens then is that our load generator will perform a DNS lookup for "www.acme.com".

The DNS lookup will end up on Amazon's DNS servers as they are hosting DNS for your domain. Amazon's DNS servers will realize that an AWS host (our load generator) wants to communicate with another AWS host (your site) and then, probably in an attempt to make communication more efficient and reduce a router hop or two, it will tell our load generator to use the AWS-internal, private IP address of your site. I.e. all EC2 machines have both an internal, private IP address used for AWS-internal communication, and an external, public IP that is used for communicating with the rest of the Internet, and Amazon tries to make machines inside AWS use the private IPs when they communicate with eachother.

The problem is that our load generator doesn't know that this is one of the very few cases where using a private IP would actually work. 99 times out of 100 it will fail because it is simply a misconfigured load test, so our load generator refuses to run tests where it has to communicate with private IP addresses. The workaround here is to use our [util.dns_remap()]({{ site.baseurl }}/3.0/user-scenarios-scripting-examples/overriding-public-dns/) function to prevent the load generator from asking DNS for the IP address in the first place.

##### Your site is using an IP in a range that has been blacklisted

Contact us to learn the reason why your particular IP has been blacklisted. Usually it is the owner of the IP address that has requested a black listing (e.g. some hosting providers don't want anyone to test sites hosted by them) and they are fully within their rights to refuse any load testing on their network so we have to comply with their wishes of course.

***

### Tests not covered by subscription

The test you are trying to run falls outside of the subscription rules. Check your VUs and test duration.

***

### Your current subscription does not cover the number of selected load zones.

Your subscription does not allow the amount of Load Zones you have selected in your test configuration. Either change your configuration or upgrade your plan.

***

### Too many tracks

You've added too many user scenarios to your test configuration. Lower the number of user scenarios. If you have a unique case that requires more, please reach out to us at support to discuss.

***

### Why don't I see XYZ metric?

The default metrics are viewable if they have been recorded during the test run. For example, if the time it takes to finish your user scenario is longer than the length of your test, you will not see a VU Load Time.

***

### General note

If you are testing a live site it is usually a good idea to run your test during low traffic hours. These are usually at night. If you do not want to stay up to run your load test you can use the scheduling functionality to schedule your test to be run when you are not around. To schedule a test, log into your account and then click “Scheduled tests” in the menu to get to the test scheduling interface. Once you are there, click “Add new scheduled test”. You will need to specify what Test configuration you wish to schedule, and what date and time you want the test to start. Note that it is very important you have correct time zone settings in your account, or you risk starting the test at another time than the intended. If you want a recurring test you can set how often the test run should repeat in the scheduling interface as well. Valid options are daily, weekly or monthly. This is useful for validating that performance has not regressed on a website that is in constant development.
