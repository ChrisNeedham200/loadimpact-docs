---
layout: classic-docs
title: Why am I experiencing a different load time than my result?
description: Reasons you may experience a different load time than your test result.
categories: [frequently-asked-questions]
order: 7
redirect_from: /knowledgebase/articles/174526-will-load-testing-transactions-show-up-on-google-a
---

***

Sometimes users load the same site that they are load testing and experience a much faster load time then what is reported by our system. This could be due to a few possible reasons:

1. Client-side (browser) caching caused resources not to be loaded. Our load generators do not simulate caching. All simulated clients appear as “new users” to the server. Refer to out article about [client-side caching in LoadImpact]({{ site.baseurl }}/3.0/frequently-asked-questions/do-virtual-users-cache-resources/) for more information
2. User is on a network that is geographically closer to the server. If your server is located far away from our load generators but close to you, traffic will have to take a longer route to reach our load generator. The extra network delay introduced can affect test results greatly, depending on the composition of your site (many small files will result in bad performance over high-latency network links). A longer route also makes it more likely that there are bandwidth bottlenecks somewhere along the way.
3. The web page can appear fully loaded graphically to the user but there might still be HTTP transactions executing in the background. Our load generators report load times that take into account all HTTP transactions on the website (as scripted in the user scenario).
