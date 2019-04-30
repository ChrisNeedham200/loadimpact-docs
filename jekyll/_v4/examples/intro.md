---
layout: classic-docs
title: Introduction to test scripting
description: An intro to k6 test scripting
categories: [examples]
order: 0
redirect_from:
    - /4.0/test-scripting/
    - /4.0/test-scripting/intro/
---

Welcome to the intro guide for scripting k6 tests.

## Features

Features of k6 that we'll take a closer look at in this section on k6 test scripting:

- Scripting in Javascript ES2015/ES6 - with support for [local and remote modules]({{ site.baseurl }}{% link _v4/core-concepts/module-imports.md %})
- [Configuration options]({{ site.baseurl }}{% link _v4/reference/test-configuration-options.md %}) as code
- [Checks]({{ site.baseurl }}{% link _v4/core-concepts/checks.md %}) and [Thresholds]({{ site.baseurl }}{% link _v4/core-concepts/thresholds.md %}) - for goal-oriented, automation-friendly load testing
- [Tags]({{ site.baseurl }}{% link _v4/core-concepts/tags.md %}) - tag requests, groups and custom metrics for more specific threshold targeting and more flexibility in result analysis
- [Environment variables]({{ site.baseurl }}{% link _v4/core-concepts/environment-variables.md %}) - change configurations and behavior of a test without changing code
- [Custom metrics]({{ site.baseurl }}{% link _v4/core-concepts/custom-metrics.md %}) - track the metrics that matter to you

**Other noteworthy features of k6**:

- Protocol support - with support for HTTP/1.1, [HTTP/2](https://docs.k6.io/docs/http2) and [WebSockets](https://docs.k6.io/docs/k6ws) out of the box
- TLS features: [client certificates](https://docs.k6.io/docs/ssl-tls-client-certificates), [configurable versions and ciphers](https://docs.k6.io/docs/ssl-tls-version-and-cipher-suites)
- [Cookies](https://docs.k6.io/docs/cookies) - simple key-value session cookies and more advanced persistent cookies
- [Crypto library](https://docs.k6.io/docs/k6crypto) - for implementing authentication/authorization protocols
- [Session recording / HAR support](https://docs.k6.io/docs/session-recording-har-support) - for converting a recorded user session (HAR file) to a load test

## Test creation options

There are four primary ways that you can create a test:

- Manually writing the code - what this section will go through
- [Record a scenario]({{ site.baseurl }}{% link _v4/guides/how-to-do-browser-recording.md %}) to get the bulk of the test logic generated for you
- [Use the HAR-to-k6 converter tool]({{ site.baseurl }}{% link _v4/guides/how-to-convert-har-to-k6-test.md %}) built-in to k6 to convert HAR files to k6 tests
- [Use the Postman-to-k6 converter tool]() to convert Postman collections to k6 tests
