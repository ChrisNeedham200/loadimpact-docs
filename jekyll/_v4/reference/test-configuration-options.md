---
layout: classic-docs
title: Test Configuration Options
description: List of available test configuration options and different ways to use them for your tests
categories: [reference]
order: 0
redirect_from: /4.0/test-scripting/test-configuration-options/
---

***

<h2>Background</h2>

The below reference is a partial list of the configuation options.  Options only available locally are omitted.  To see a full list, please refer to the [k6 documentation](https://docs.k6.io/docs/options).

***

Options allow you to configure how k6 will behave during test execution. For most options, you can specify the specific option in four different ways:

- As part of your script code, so it is version controlled
- As a command line flag
- As a environment variable
- As part of a config file.

There is an order of precedence when evaluating options, which is as follows: defaults < config file < exported script options < environment variables < command-line flags. Options from a greater level can be used to overwrite those from a lower level. CLI flags have the greatest priority.

For example, duration can be specified in 4 different ways:

- use the command-line flag `-d 10s`
- define `K6_DURATION` as an environment variable
- set the `duration: "10s"` option in the script
- set the `duration: "10s"` option in the config file

Here is an example of a test with configurations set within the script:

{% highlight js linenos %}
import http from "k6/http";

export let options = {
  hosts: {
    "test.loadimpact.com": "1.2.3.4"
  },
  stages: [
    { duration: "1m", target: 10 },
    { duration: "1m", target: 20 },
    { duration: "1m", target: 0 }
  ],
  thresholds: {
    http_req_duration: ["avg<100", "p(95)<200"]
  },
  noConnectionReuse: true,
  userAgent: "MyK6UserAgentString/1.0"
};

export default function() {
  http.get("http://test.loadimpact.com/");
}

{% endhighlight %}

Here are the same options set via a `config.json` file

{% highlight json linenos %}

{
  "hosts": {
    "test.loadimpact.com": "1.2.3.4"
  },
  "stages": [
    { "duration": "1m", "target": 10 },
    { "duration": "1m", "target": 30 },
    { "duration": "1m", "target": 0 }
  ],
  "thresholds": {
    "http_req_duration": ["avg<100", "p(95)<200"]
  },
  "noConnectionReuse": true,
  "userAgent": "MyK6UserAgentString/1.0"
}

{% endhighlight %}

Or, set the options via environment variables or command-line flags:

```shell

$ K6_NO_CONNECTION_REUSE=true K6_USER_AGENT="MyK6UserAgentString/1.0" k6 run ~/script.js

$ k6 run ---no-connection-reuse --user-agent "MyK6UserAgentString/1.0" ~/script.js

```

**Select the options you wish to learn more about from the following table:**
- TOC
{:toc}

## duration

**What it is:** A string specifying the total duration a test run should be run for. During this time each Virtual User will execute the script (default function) in a loop.

**How to set it:** `duration` or `--duration value` or `-d value` or `K6_DURATION`

**Default value:** null

**Example:**

{% highlight js linenos %}
export let options = {
    duration: "3m"
};
{% endhighlight %}

***

## ext

**What it is:** An object used to set configuration options for the LoadImpact cloud service

### distribution

When running a cloud execution test you can configure from which load zones the traffic should be generated. See the [cloud execution]({{ site.baseurl }}{% link _v4/guides/cloud-execution.md %}) article for more information.


### Name

**What it is:** A string specifying the name of your test in the LoadImpact cloud.

**How to set it:** In your test script

**Default value:** filename of your script.

**Example:**

{% highlight js linenos %}
export let options = {
    ext: {
        loadimpact: {
            name: "My test name"
        }
    }
};
{% endhighlight %}

### project


By default tests and test runs will be created and run under your default project, in your default organization. To create and run tests under a different project, whether under your default organization or one you've been invited to, you have two options:

1. You can specify the project ID in the script options:
{% highlight js linenos %}
export let options = {
    ext: {
        loadimpact: {
            projectID: 123456
        }
    }
}
{% endhighlight %}
<ol start="2"><li>You can set the <code>K6_CLOUD_PROJECT_ID</code> environment variable when running the test.</li></ol>

You find the ID of a LoadImpact project by selecting a k6 project in the UI and looking in the URL bar of your browser, the `12345` in `https://app.loadimpact.com/projects/12345/dashboard` is the project ID.

***

## hosts

**What it is:** An object that overrides DNS resolution. This behaves similarly to what you can do with `/etc/hosts` on Linux/Unix or `C:\Windows\System32\drivers\etc\hosts` on Windows. In the example below, all requests to `test.loadimpact.com` will be routed to `1.2.3.4`. The HTTP `Host` header will still be set to `test.loadimpact.com`

**How to set it:** `hosts`

**Default value:** null

**Example:**

{% highlight js linenos %}
export let options = {
    hosts: {
        "test.loadimpact.com": "1.2.3.4"
    }
};
{% endhighlight %}

***

## insecureSkipTLSVerify

**What it is:** A boolean, `true` or `false`. When this option is set to `true`, all of the verifications that would normally be done to establish trust in a server provided TLS certificate are ignored.

**How to set it:** `insecureSkipTLSVerify` or `--insecure-skip-tls-verify` or `K6_INSECURE_SKIP_TLS_VERIFY`

**Default value:** false

**Example:**

{% highlight js linenos %}
export let options = {
    insecureSkipTLSVerify: true
};
{% endhighlight %}

***

## maxRedirects

**What it is:** An integer, the maximum number of HTTP redirects that k6 will follow before erroring out the request.

**How to set it:** `maxRedirects` in options or `--max-redirects value` as a command line flag, or `K6_MAX_REDIRECTS` as a environment variable

**Default value:** 10

**Example:**

{% highlight js linenos %}
export let options = {
    maxRedirects: 10
};
{% endhighlight %}

***

## batch

**What it is:** An integer, the maximum number of parallel connections that each Virtual User can make in a `http.batch()` call within a script. If you have a `batch()` call that you've given 20 URLs and `batch` is set to 15. Then each Virtual User will make 15 requests in parallel and queue the rest. The remainder are executed as previous requests finish.

**How to set it:** `batch` in options or `--batch` as a command line flag, or `K6_BATCH` as a environment variable

**Default value:** 10

**Example:**

{% highlight js linenos %}
export let options = {
    batch: 15
};
{% endhighlight %}

***

## batchPerHost

**What it is:** An integer, the maximum number of parallel connections per host that each Virtual User can make in a `http.batch()` call within a script. This option is similar to `batch`, except it restricts requests based on destination host.

**How to set it:** `batchPerHost` in options or `--batch-per-host` as a command line flag, or `K6_BATCH_PER_HOST` as a environment variable

**Default value:** No defined limit / Equal to the value in `batch`

**Example:**

{% highlight js linenos %}
export let options = {
    batchPerHost: 10
};
{% endhighlight %}

***

## discardResponseBodies

**What it is:** A boolean, `true` or `false`. When this option is set to `true`, all of the response bodies sent will be discarded. The response is still fully downloaded, however, it is not saved to memory.

**How to set it:** `discardResponseBodies` in options or `--discard-response-bodies` as a command line flag, or `K6_DISCARD_RESPONSE_BODIES` as a environment variable

**Default value:** false

**Example:**

{% highlight js linenos %}
export let options = {
  discardResponseBodies: true,
};
{% endhighlight %}

For cases where you need to save a response from individual requests, you may save those by setting a `responseType` with the request.

{% highlight js linenos %}
import http from 'k6/http';
export let options = {
  discardResponseBodies: true,
};
export default function () {
  let response = http.get("http://test.loadimpact.com", { responseType: "text" });
  // ... do something with the response, but ignore the contents of static files:
  http.batch([
    "http://test.loadimpact.com/images/logo.png",
    "http://test.loadimpact.com/style.css"
  ]);
};
{% endhighlight %}

Note: `responseType` may be set to `text` (default), `binary`, or `none`.  Alternatively, you can use `none` to discard the response body for individual requests.

***

## noConnectionReuse

**What it is:** A boolean, true or false, specifying whether k6 should disable keep-alive connections

**How to set it:** `noConnectionReuse` in options or `--no-connection-reuse` as a command line flag, or `K6_NO_CONNECTION_REUSE` as a environment variable

**Default value:** false

**Example:**

{% highlight js linenos %}
export let options = {
    noConnectionReuse: true
};
{% endhighlight %}

***

## noVUConnectionsReuse

**What it is:** A boolean, true or false, specifying whether k6 should reuse TCP connections between iterations of a Virtual User

**How to set it:** `noVUConnectionReuse` in options or `--no-vu-connection-reuse` as a command line flag, or `K6_NO_VU_CONNECTION_REUSE` as a environment variable

**Default value:** false

**Example:**

{% highlight js linenos %}
export let options = {
    noVUConnectionReuse: true
};
{% endhighlight %}

***

## paused

**What it is:** A boolean, true or false, specifying whether the test should start in a paused state. To resume a paused state, you would use the `k6 resume` command

**How to set it:** `paused` in options or `--paused` or `-p` as a command line flag, or `K6_PAUSED` as a environment variable

**Default value:** false

**Example:**

{% highlight js linenos %}
export let options = {
    paused: true
};
{% endhighlight %}

***

## rps

**What it is:** An integer, the maximum number of requests to make per second across all Virtual Users. Note: This is an upper limit that tests will not exceed. This can not be used to set an arrival rate or RPS in a test.

**How to set it:** `rps` in options or `--rps` as a command line flag, or `K6_RPS` as a environment variable

**Default value:** 0 (unlimited)

**Example:**

{% highlight js linenos %}
export let options = {
    rps: 500
};
{% endhighlight %}

***

## stages

**What it is:** A list of objects that specify the target number of Virtual Users to ramp up or down for a specific period. Ramping is done linearly over the set period of time.

**How to set it:** `stages` in options or `--stage DURATION:TARGET` or `-s DURATION:TARGET` as a command line flag, or `K6_STAGES` as a environment variable

**Default value:** based on `vus` and `duration`

**Examples:**

{% highlight js linenos %}
export let options = {
    stages: [
        { duration: "3m", target: 10 }, // Ramp up to 10 VUs over 3 minutes
        { duration: "5m", target: 10 }, // Stay at 10 VUs for 5 minutes
        { duration: "10m", target: 35 }, // Ramp up to 35 VUs over 10 minutes
        { duration: "3m", target: 0 }, // Ramp down to 0 VUs over 3 minutes
    ]
};
{% endhighlight %}

Multiple stages set from command-line flag or environment variables are comma delimited.

```shell
$ k6 run --stage 5s:10,5m:20,10s:5 ~/script.js

$ K6_STAGES="5s:10,5m:20,10s:5" k6 run ~/script.js
```

***

## tags

**What it is:** Tags that should be set test wide across all metrics. Tags with the same name that are specified on a request, check, or custom metric will take precedence over a test wide tag.

**How to set it:** `tags` in options or `--tag NAME:VALUE` as a command line flag

**Default value:** null

**Example:**

{% highlight js linenos %}
export let options = {
    tags: {
        "name": "value"
    }
};
{% endhighlight %}

***

## throw

**What it is:** A boolean, true or false, to specify if k6 should throw errors on failed HTTP requests

**How to set it:** `throw` in options or `--throw` or `-w` as a command line flag, or `K6_THROW` as a environment variable

**Default value:** false

**Example:**

{% highlight js linenos %}
export let options = {
    throw: true
};
{% endhighlight %}

***

## blacklistIPs

**What it is:** An object containing the ranges of IPs to blacklist from tests. LoadImpact maintains it's own blacklist which contains local, loopback, LoadImpact specific IPs, and other IPs

**How to set it:** `blacklistIPs` in options or `--blacklist-ip` as a command line flag, or `K6_BLACKLIST_IPS` as a environment variable

**Default value:** null

**Example:**

{% highlight js linenos %}
export let options = {
    blacklistIPs: ["10.0.0.0/8"]
};
{% endhighlight %}

***

## summaryTrendStats

**What it is:** Define stats for trend metrics (response times), one or more as 'avg,p(95),...'

**How to set it:** `summaryTrendStats` in options or `--summary-trend-stats` as a command line flag, or `K6_SUMMARY_TREND_STATS` as a environment variable

**Default value:** null

**Example:**

{% highlight js linenos %}
export let options = {
    summaryTrendStats: ["avg","p(95)"]
};
{% endhighlight %}

***

## tlsAuth

**What it is:** A list of TLS client certificate configuration objects. Each object needs to specify the host(s)/domain(s) the given client certificate is valid for

**How to set it:** `tlsAuth` in options

**Default value:** null

**Example:**

{% highlight js linenos %}
export let options = {
    tlsAuth: [
        { domains: ["example.com"],
          cert: open("mycert.pem"),
          key: open("mycert-key.pem") }

    ]

};
{% endhighlight %}

***

## tlsCipherSuites

**What it is:** A list of cipher suits allowed to be used in SSL/TLS interactions with a server. For a full list of ciphers refer to [this GO documentations](https://golang.org/pkg/crypto/tls/#pkg-constants)

**How to set it:** `tlsCipherSuites` in options

**Default value:** null (all supported cipher suites are allowed)

**Example:**

{% highlight js linenos %}
export let options = {
    tlsCipherSuites: [
        "TLS_RSA_WITH_RC4_128_SHA",
        "TLS_RSA_WITH_AES_128_GCM_SHA256",
    ]
};
{% endhighlight %}

***

## tlsVersion

**What it is:** A string specifying the only SSL/TLS version allowed or object representing the min/max SSL/TLS versions allowed.

**How to set it:** `tlsVersion` in options

**Default value:** null (all supported versions are allowed)

**Example:**

{% highlight js linenos %}
Specifying a specific version to allow:

export let options = {
    tlsVersion:  "tls1.2"
};
Specifying a min and max version to allow:

export let options = {
    tlsVersion: {
        min: "ssl3.0",
        max: "tls1.2"
    }
};
{% endhighlight %}


***

## userAgent

**What it is:** String specifying the user-agent string to use in `User-Agent` headers when making HTTP requests. Custom user-agent strings are often used as a way to [whitelist traffic from the LoadImpact cloud infrastructure](/4.0/how-to-tutorials/how-to-open-firewall-to-load-impact-only/#opening-up-your-firewall-to-all-ips-potentially-used-in-the-test)

**How to set it:** `userAgent` in options or `--user-agent` as a command line flag, or `K6_USER_AGENT` as a environment variable

**Default value:** "k6/0.20 (https://k6.io/)" Version number is dependent on the version of k6 being used.

**Example:**

{% highlight js linenos %}
export let options = {
    userAgent: "MyK6UserAgentString/1.0"
};
{% endhighlight %}

***

## httpDebug

**What it is:**

**How to set it:** `httpDebug` in options or `--http-debug` as a command line flag, or `K6_HTTP_DEBUG` as a environment variable

**Default value:**

**Example:**

{% highlight js linenos %}
export let options = {
    httpDebug: "full"
};
{% endhighlight %}

***

## vus

**What it is:** An integer, specifying the number of Virtual Users to run concurrently. Refer to the stages option for more flexibility and control over Virtual Users in your test.

**How to set it:** `vus` in options or `--vus value` or `-u value` as a command line flag, or `K6_VUS` as a environment variable

**Default value:** 1

**Example:**

{% highlight js linenos %}
export let options = {
    vus: 10
};
{% endhighlight %}

***

## vusMax

**What it is:** An integer, specifying the maximum number of Virtual Users. This option is typically used when you intend to dynamically scale Virtual Users up and down using the `k6 scale` command. This is provided as instantiating a VU is an expensive operation. This option allows you to preallocate Virtual Users.

**How to set it:** `vusMax` in options or `--max value` or `-m value` as a command line flag, or `K6_VUS_MAX` as a environment variable

**Default value:** 0

**Example:**

{% highlight js linenos %}
export let options = {
    vusMax: 10
};
{% endhighlight %}

***

## systemTags

**What it is:** Specify the [system tags](https://docs.k6.io/docs/tags-and-groups#section-system-tags) to be collected in metrics. The LoadImpact cloud requires certain system tags to be used.

**How to set it:** `systemTags` in options or `--system-tags` as a command line flag, or `K6_SYSTEM_TAGS` as a environment variable

**Default value:** `proto`, `subproto`, `status`, `method`, `url`, `name`, `group`, `check`, `error`, `tls_version`

**Example:**

{% highlight js linenos %}
code here
{% endhighlight %}

***

## setupTimeout

**What it is:** Specify how long the `setup()` function is allowed to run before it's terminated the test fails

**How to set it:**

**Default value:** 10s

**Example:**

{% highlight js linenos %}
code here
{% endhighlight %}

***

## teardownTimeout

**What it is:** Specify how long the `teardown()` function is allowed to run before it's terminated the test fails

**How to set it:**

**Default value:** 10s

**Example:**

{% highlight js linenos %}
code here
{% endhighlight %}

***

## config.json

Specify the config file in JSON format to read the options values. If the config file is not specified, k6 will look for `config.json` in the `loadimpact/k6` directory inside the regular directory for configuration files on the operating system.

For example in Linux/BSDs, it will look for `config.json` inside ``${HOME}/.config/loadimpact/k6`


***

See also:
- k6 docs on [options](https://docs.k6.io/docs/options) for additional options available in local execution modes.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTI0MTIwNTEwMF19
-->
