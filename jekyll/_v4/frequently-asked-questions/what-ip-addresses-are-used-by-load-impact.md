---
layout: classic-docs
title: What IP Addresses are used by LoadImpact to generate load for cloud based tests?
description: List and explanation of the IP addresses LoadImpact uses to generate load during a k6 cloud executed test.
categories: [frequently-asked-questions]
order: 1
---

***

<h2>Purpose</h2>

This article contains the various IP addresses LoadImpact uses to generate load for cloud based tests and other services. The most common reason for needing this information is to open your firewall to allow tests to reach the staging/test environment from a cloud based test. If you are streaming results to Insights utilizing `k6 run myscript.js -o cloud` you may only need to allow traffic against ingest.loadimpact.com on port 443.

Other methods, such as header or query parameter whitelisting, may also fit your requirements. Refer to [this article]({{ site.baseurl }}/4.0/how-to-tutorials/how-to-open-firewall-to-load-impact-only/) for more information on those methods.

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
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE3MjE3OTMwMjZdfQ==
-->
