---
layout: classic-docs
title: Load Testing Postman Collections
description: How to load test your Postman collections and convert them to k6 test scripts to use in LoadImpact
categories: [guides]
order: 4
redirect_from:
  - /4.0/how-to-tutorials/load-testing-with-postman-collections/
  - /4.0/guides/load-testing-with-postman-collections/
redirect_to: https://blog.loadimpact.com/load-testing-with-postman-collections

---

***

## Background

<div class="callout callout-warning" role="alert">
  <b>New Postman converter available in Beta release!</b><br>
  A new and improved Postman to LoadImpact converter has recently been released.
  In order to use the new converter, please clone the repository <a href="https://github.com/bookmoons/postman-to-k6/tree/bookmoons/improve">here</a>, install dependencies with `npm install` then use `node bin / postman-to-k6 collection.json -o k6-script.js`, where the collection JSON is your collection and the JavaScript is the output.  If you wish to use the existing version, please read below.
</div>



<p style="text-align: center;"><img src="{{ site.baseurl }}/assets/img/v4/how-to-tutorials/postman-logo.png" alt="Postman" width="300"/></p>

[Postman](https://getpostman.com/) is one of the best-in-market tools for functional testing of APIs. As a Postman user, you organize your API tests into collections of requests. While this is great for functional testing, it's also important to understand how API endpoints perform under load.

The presented information covers how to use the Portman to LoadImpact converter with version 4.0 of LoadImpact. Version 4.0 is based on the open source load generator, k6.

## How to use the Postman to LoadImpact converter

LoadImpact provides a [CLI tool for converting Postman collections to k6 tests](https://github.com/loadimpact/postman-to-k6).

## Installation and usage

The following section describes the steps to convert your Postman collections to k6 tests.

1. First, you have to export your Postman collections.
    ![Download collection from Postman]({{ site.baseurl }}/assets/img/v4/how-to-tutorials/postman-download-collection.png)

2. Install the command-line tool:

    `npm install -g postman-to-k6`

    For more installation options see the [Github repo](https://github.com/loadimpact/postman-to-k6).

3. Run the tool to convert the Postman collection

    `postman-to-k6 path/to/postman-collection.json -o path/to/k6-script.js`

    `k6-script.js` will be the outputted script name in the specified path of your output.
4. Make any edits to your script necessary

    You may wish to change [configuration options]({{ site.baseurl }}{% link _v4/reference/test-configuration-options.md %})
    Create [custom metrics]({{ site.baseurl }}{% link _v4/core-concepts/custom-metrics.md %})
    Define [thresholds]({{ site.baseurl }}{% link _v4/core-concepts/thresholds.md %})

5. Utilize your script with your LoadImpact account
    `k6 run k6-script.js` will execute your script locally with local output
    `k6 run k6-script.js -o cloud` will execute locally with cloud output to the LoadImpact platform
    `k6 cloud k6-script.js` will package up the script and any dependencies for execution on LoadImpact's infrastructure

**See also**:
- [How to do a browser recording]({{ site.baseurl }}{% link _v4/guides/how-to-do-browser-recording.md %})
- [How to convert HAR files to k6 tests]({{ site.baseurl }}{% link _v4/guides/how-to-convert-har-to-k6-test.md %})
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTg0MTkwMDkzMV19
-->
