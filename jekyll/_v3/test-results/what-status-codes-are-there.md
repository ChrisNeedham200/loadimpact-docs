---
layout: classic-docs
title: Will status codes are reported back on URLs in a test?
description: Explanation of the status codes returned for URLs in a test.
categories: [test-results]
order: 6
redirect_from: /knowledgebase/articles/174137-what-status-codes-are-there
---

***



LoadImpact stores the HTTP status/return codes on a per-URL basis, and reports for each individual URL how many times it has seen a particular status code from the server, and what response times (avg/min/max) it got for that particular code.

An example: A load test ramping up from 1 to 5000 concurrent users that contains a user scenario that loads “http://some.dom.ain/index.html”. At the start, when the load level is low, the web server being tested performs as it should and returns HTTP 200 codes plus the HTML file that was requested, but when the load level passes 4000 concurrent users, the server starts to run out of some resource (e.g. database connections) and begins returning “500 server error” instead of the expected content. Towards the end of the test the 500-codes (errors) occur more and more frequently. This means that we might end up with a test result where the “index.html” resource got e.g. 23,567 responses with the status code “200” and 12,856 responses where the status code was “500” and no content was returned. Most of the 200-codes occured early in the test while the 500-codes occured in the later stages, when the load level was higher.

If you want to see how fast the server is delivering content at various points during the test you can choose to plot the average transaction response time for the 200-codes. If you want to see the error rate throughout the test you can plot the “count” (number of times a response code was seen) metric for the 500-codes (perhaps combine it with the “count” value for the 200-codes in the same chart and you can see both the successful and the failed transactions next to eachother).

For more info on HTTP status codes, Refer to: [List of HTTP Status Codes](http://en.wikipedia.org/wiki/List_of_HTTP_status_codes)

There are also some 4-digit (non-HTTP) status codes that can be found in test results. They are related to the lower level networking functionality. Here is a list of the most common ones:


Code | Reason                | definitions
-----|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
1101 | Unsupported protocol  | The URL used a protocol that we do not support
1106 | Couldn’t resolve host | We failed to find the IP adress of the remote host in the DNS
1107 | Couldn’t connect      | Failed to connect to target server
1118 | Partial file          | A file transfer was shorter or larger than expected. This happens when the server first reports an expected transfer size, and then delivers data that doesn’t match the previously given size
1128 | Operation timed out   | Nothing happened for a long time, causing the transaction to fail/time out
1135 | SSLconnect error      | A problem occurred somewhere in the SSL/TLS handshake
1147 | Too many redirects    | A resource was redirected more than 5 times. We only follow a maximum of 5 redirects
1152 | Got nothing           | We expected some data from the server but got nothing
1155 | Send error            | Failed sending network data
1156 | Receive error         | Failure while receiving network data
{: class="table table-bordered"}
