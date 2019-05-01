---
layout: classic-docs
title: What does the map show?
description: Explanation of the map shown within the LoadImpact test result dataset
categories: [test-results]
order: 4
redirect_from: /knowledgebase/articles/173853-what-does-the-map-show
---

***


The map shows the geographical location of the target system and the load generators used in the test. You can see on the map the origin of the load generator/s you have configured in your test configuration.

**Note**:  At times, the location identified could be incorrect. This is typically due to the geolocation data on file for that IP address or because another request was made first. i.e. if your test starts with a request against google, the map will likely show the target server as Mountainview, California. You can safely ignore any incorrect display of location


![LoadImpact Test Result Map]({{ site.baseurl }}/assets/img/v3/test-result/test-result-map.jpg)
