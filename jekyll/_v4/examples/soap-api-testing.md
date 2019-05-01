---
layout: classic-docs
title: Load Testing a SOAP API
description: How to load test a SOAP based API
categories: [examples]
order: 7
---

***

<h2>Background</h2>

Scripting example on how to load test SOAP APIs.

***

## SOAP
Althought k6 doesn't have any builtin APIs for working with SOAP or XML data in general, you can still easily load test a SOAP based API by crafting SOAP messages and using the HTTP request APIs.

### Making SOAP requests
{% include 4.0/scripting-examples/http-soap-req.md %}
