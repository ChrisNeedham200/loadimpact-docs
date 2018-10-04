---
layout: classic-docs
title: Why should I filter domains in my load test?
description: Reasons why you should filter domains in your load test and how to achieve that both in-app and using k6 locally
categories: [frequently-asked-questions]
order: 2
---

***

<h1>Purpose</h1>

This article explains the reasons why you should filter domains within your load test, how to filter domains and some cases where you may actually not want to filter domains.

## Background

Nearly all apps, sites, and services on the web make requests to external resources. It's likely that an app or site you are testing includes any or all of the following: CDNs, marketing trackers (Facebook, Twitter, etc.), analytics tools (Google Analytics), marketing automation trackers (Hubspot, Marketo, Eloqua), developer tools (Fullstory, HotJar, etc.) and more.


## Why should I filter domains?

You should filter your domains for all or some of the following reasons:

- External resources may generate unique IDs, which could produce a lot of extra URLs. This would make reviewing results harder to parse through
- External resources may throttle your requests generated from a load test, this could produce skewed results or even trigger a threshold when it should not have been.
- External resources typically don't want to be a part of a load test and including them may violate your TOS with that third party
- In most cases, even if you did notice a performance problem with a 3rd party you normally will have no way to fix that issue
- Most CDNs charge based on usage, so making requests against the CDN could have financial costs associated with the test. _Note:_ You may have a valid reason to test your CDN. We have seen users specifically test CDNs in the past for various reasons.

## Filtering test scripts created in-app
There are a few ways to filter your domains with Load Impact. If you are using the URL analyzer or HAR file upload in-app, you can utilize the `Domain` option. This allows you to enter domains you specifically want to include within your test. e.g. If I was testing `app.example.com` I would use `example.com` in the `Domain` option.

For both the in-app URL analyzer and HAR file upload, we would ignore requests to other domains when creating the script.

If you are scripting in-app, it's unlikely you would manually write those third party requests, therefore no filter option is needed

## Filtering when using k6's built in HAR file converter

If you are not using one of the in-app options to create your script, you are likely using the built in HAR file converter available in k6. You can convert a HAR file from the command line using `k6 convert myHarFile.har -O myScript.js`. This command would convert the entire contents of the target HAR file in a script with the output denoted.

In addition to being able to convert HAR files, there are multiple flags available to aid in filtering the file. Please refer to  [How to convert HAR to k6]({{ site.baseurl }}/4.0/how-to-tutorials/how-to-convert-har-to-k6-test/) for more information on this feature.

## See Also:

- [How to do a browser recording]({{ site.baseurl }}/4.0/how-to-tutorials/how-to-do-browser-recording/)
- [How to convert HAR to k6]({{ site.baseurl }}/4.0/how-to-tutorials/how-to-convert-har-to-k6-test/)
