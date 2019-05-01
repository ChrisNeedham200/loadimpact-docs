---
layout: classic-docs
title: What IP Addresses are used by LoadImpact's to generate load?
description: List and explanation of the IP addresses LoadImpact's Lua based product uses to generate load during a test.
categories: [frequently-asked-questions]
order: 5
redirect_from:
  - /knowledgebase/articles/174262-what-load-zones-and-ip-addresses-does-load-impact
  - /knowledgebase/articles/174524-where-do-you-generate-the-load-traffic-from
---

***
This article contains the information regarding the various IP addresses LoadImpact will use during a test, validation or script creation.

If you are looking to run tests behind a firewall completely, you may want to consider our new open source load generator, k6. k6 allows you to run tests locally and stream those results into LoadImpact Insights, for analysis. To get started with k6, please refer to our [Quick Start Guide]({{ site.baseurl }}{% link _v4/getting-started/local-test-execution-hello-world.md %})


If you are unable to whitelist a large ranges of IPs, you may want to consider another method, such as header or query parameter whitelisting, as described in [this article]({{ site.baseurl }}/3.0/how-to-tutorials/how-to-open-firewall-to-load-impact-only/)

**The main loadimpact.com IP address:**

52.4.55.233 - Ashburn

This used for validations, and must be included in every test, regardless of loadzone. Please refer to the Load Zone section of this document for the full range of IP addresses used when testing.

**Auto-generated load scripts/Page Analyzer IP address:**

54.208.147.247

This is only for URL generated tests from within the LoadImpact platform.

**Proxy Recorder IP addresses:**

The Session Recorder IP address is not fixed. It does not change often, but it is a good practice to re-resolve the hostname and make necessary changes to the firewall if this is fixed to the IP address. We highly recomend adding the hostname proxy.loadimpact.com instead of the IP address if possible.
IP address for Session Recorder (Updated on 11/09/2014): 54.88.120.157

Note: Please ensure that your servers are allowed outbound connections within the port range 20000 - 21000 as well.


### Load Zones

LoadImpact uses AWS for cloud load generators. For the IP addresses used in the different load zones and filtering methods please refer directly to [Amazon](http://docs.aws.amazon.com/general/latest/gr/aws-ip-ranges.html).

If you prefer to view the ranges directly, within the above link, the [ip-ranges.json](https://ip-ranges.amazonaws.com/ip-ranges.json) file provides the updated list of IP addresses used by our load generators. In order to know which IP range/s can be used, you need to filter the `service` of type EC2 and the `region` of the selected load zone/s in your test configuration.

The zone codes are mapped as follows:


Code           | Name
---------------|--------------------------
us-east-1      | US East (Ashburn)
us-east-2      | US East (Columbus)
us-west-1      | US West (Palo Alto)
us-west-2      | US West (Portland)
ca-central-1   | Canada (Montreal)
eu-west-1      | EU (Dublin)
eu-central-1   | EU (Frankfurt)
eu-west-2      | EU (London)
ap-northeast-1 | Asia Pacific (Tokyo)
ap-northeast-2 | Asia Pacific (Seoul)
ap-southeast-1 | Asia Pacific (Singapore)
ap-southeast-2 | Asia Pacific (Sydney)
ap-south-1     | Asia Pacific (Mumbai)
sa-east-1      | South America (São Paulo)
{: class="table table-bordered"}
