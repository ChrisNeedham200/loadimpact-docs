---
layout: classic-docs
title: Converting HAR Files to Test Scripts
description: Guide on converting HAR files on the command line using k6 to create load testing scripts
categories: [guides]
order: 4
redirect_from: /4.0/how-to-tutorials/how-to-convert-har-to-k6-test/
redirect_to: https://docs.k6.io/docs/session-recording-har-support
---

***

# Purpose

Tutorial and explanation on how to utilize the built k6's built in HAR file coverter and associated options. The built-in HAR converter that will read HAR files and convert them to k6 scripts that can then be executed.

## Using the converter

To use the converter use the following command:

`k6 convert myfile.har`

The above command will make k6 read myfile.har, convert it into k6-compatible Javascript and then output it on stdout (you can use the `-O` option to save to a file, or just redirect the output on stdout to a file).


## k6 converter options

The converter offers various options to aid in producing a script, without extra noise. You can view all options with `k6 help convert`

### `-O, --output`
This allows you to specify the name of the output file. The default is "har-script.js"

### `--only`
This option allows you to supply a comma-separated list of domains which are the only ones you want to fetch things from in your k6 test. This means that k6 will filter out any requests that go to domains other than these.

### `--skip`
The inverse of `--only`. This option allows you to specify some domains that you want to exclude from the k6 test, meaning that the generated k6 script will not contain any requests for this domain.

### `--batch-threshold`

When executing tests of websites or web apps, you want Virtual Users to make requests like a browser would. Browsers open connections in parallel and load those resources in parallel. By default, the converter looks for requests made within 500 ms of one another and includes them in the same batch request in your script.

This flag allows you to specify the maximum duration between requests. e.g. `--batch-threshold 1000` would change the setting to all requests made within 1 second of each other, are put in the same batch request in the outputted script. If there is a delay of longer than 1 second, a new batch request is created in the script.


## Tools that can output HAR files

The built-in HAR converter is very useful because HAR is a format supported by many other tools, whose output can now be used by k6 to control VU behavior. Here is a list of some useful tools that are able to output HAR files:

- [Chrome](https://www.google.com/chrome/) (browser)
- [Firefox](https://www.mozilla.org/en-US/firefox/) (browser)
- [Microsoft Edge](https://www.microsoft.com/en-us/windows/microsoft-edge) (browser)
- [Charles recording proxy](http://www.charlesproxy.com/) (HTTP proxy/recorder)
- [Dynatrace](https://www.dynatrace.com/) (APM tool)
- [Fiddler](http://www.telerik.com/fiddler) (HTTP proxy/recorder)
- [Webpagetest](http://www.webpagetest.org/) (Online web page tester)
- [Crossbrowsertesting](http://crossbrowsertesting.com/) (Online testing service)
- [pcap2har](https://github.com/andrewf/pcap2har) (Converts sniffer - e.g. Wireshark - pcap files to HAR)

**See also**:
- [How to do a browser recording]({{ site.baseurl }}/4.0/guides/how-to-do-browser-recording)
- [How to load test with Postman collections]({{ site.baseurl }}/4.0/guides/load-testing-with-postman-collections)
- [Session recording/HAR support](https://docs.k6.io/docs/session-recording-har-support)
