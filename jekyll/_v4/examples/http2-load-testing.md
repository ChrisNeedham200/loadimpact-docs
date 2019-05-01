---
layout: classic-docs
title: Load Testing with HTTP/2
description: How to load test over HTTP/2
categories: [examples]
order: 6
---

***

<h2>Background</h2>

Information on how to load test HTTP/2 with LoadImpact.

***

## HTTP/2
In k6 HTTP/2 is automatic. If the target system indicates that a connection can be upgraded from HTTP/1.1 to HTTP/2, k6 will do so automatically.

### Making HTTP/2 requests
{% include 4.0/scripting-examples/http-http2-req.md %}
