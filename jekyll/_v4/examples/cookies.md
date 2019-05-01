---
layout: classic-docs
title: Manually Handling Cookies
description: Code samples of ways you can access and handle cookies in your load test
categories: [examples]
order: 2
---

***

<h2>Background</h2>

Scripting examples on how you can interact with cookies during your load test, if required.

***


## Cookies
As HTTP is a stateless protocol, cookies are used by server-side applications to persist data on client machines. This is used more or less everywhere on the web, commonly for user session tracking purposes. In k6 cookies are managed automatically by default, however there're use cases where access to read and manipulate cookies are required.

### Accessing a cookie set by server in response headers
{% include 4.0/scripting-examples/http-cookies-access.md %}

### Logging all cookies in response
<div class="callout callout-warning" role="alert">
    <b>Note that this only works when using k6 locally</b><br>
    The <code>console.log()</code> family of APIs are currently only useful when running k6 locally.
    When running k6 tests with LoadImpact Cloud Execution the logs will be discarded.
</div>

{% include 4.0/scripting-examples/http-cookies-log-all-in-resp.md %}

### Setting a cookie in VU cookie jar
To set a cookie that should be sent with every request matching a particular domain, path etc. you'd do something like this:
{% include 4.0/scripting-examples/http-cookies-set-cookie-in-jar.md %}

**Relevant k6 APIs**:
- [http.CookieJar](https://docs.k6.io/docs/cookiejar-k6http)
    - [set(url, name, value, [additionalProps])](https://docs.k6.io/docs/cookiejarsetname-value-options)
- [http.cookieJar()](https://docs.k6.io/docs/cookiejar)
