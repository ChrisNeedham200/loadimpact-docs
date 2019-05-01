---
layout: classic-docs
title: Module imports
description: Modularizing code makes it easier to understand and refactor. LoadImpact supports modulararity of load test scripts.
categories: [core-concepts]
order: 11
redirect_from:
  - /4.0/test-scripting/modules-imports/
  - /4.0/core-concepts/modules-imports
---

***

<h2>Background</h2>

LoadImpact 4.0 and k6 come with a batteries included standard library.  They also support importing external libraries. This article provides an explanation and code samples on how to import the included or external modules into your k6 script.

_**Best Practice Alert:**_ We recommend that you start building out you own custom library for common actions you find yourself doing in your test scripts.  This is especialy important to make your efforts scalable to the rest of your team.

**Note:** External libraries are only available for import when using k6 locally to trigger tests.  When you execute your test using the `k6 cloud` command, k6 will automatically bundle up the dependencies into an archive and upload that to our cloud service.  You can rerun these tests through the web interface.


- TOC
{:toc}

***

## Modules
Load testing sophisticated applications requires advanced scripting capabilities, k6 supports powerful modules feature allowing you to:

- modularize your code.
- reuse existing Javascript libraries.

k6 can load ES6 modules and ES5 libraries.

Refer to k6 docs on [modules](https://docs.k6.io/docs/modules) for additional information.

### Builtin modules
There are a number of builtin modules in k6 with performance testing related APIs.  These are available both in the web based script editor and when using k6 locally.

- [k6](https://docs.k6.io/docs/k6)
  - The k6 module contains k6-specific functionality.
- [k6/crypto](https://docs.k6.io/docs/k6crypto)
  - The k6/crypto module provides common hashing functionality available in the GoLang [crypto](https://golang.org/pkg/crypto/) package.
- [k6/encoding](https://docs.k6.io/docs/k6encoding)
  - The encoding module provides base64 encoding/decoding as defined by RFC4648.
- [k6/html](https://docs.k6.io/docs/k6html)
  - The k6/html module contains functionality for HTML parsing.
- [k6/http](https://docs.k6.io/docs/k6http)
  - The k6/http module contains functionality for performing HTTP transactions.
- [k6/metrics](https://docs.k6.io/docs/k6metrics)
  - The metrics module provides functionality to create custom metrics
- [k6/ws](https://docs.k6.io/docs/k6-websocket-api)
  - The ws module provides a WebSocket client implementing the WebSocket protocol.

{% highlight js linenos %}
//Example import of built in Modules
import http from "k6/http";
import { Counter, Gauge, Rate, Trend} from "k6/metrics";
import { check } from "k6";
import { sha256 } from "k6/crypto";
{% endhighlight %}

Full documentation of these modules can be found within the [k6 docs](https://docs.k6.io/docs/)

## Remote modules

k6 can import modules that are hosted remotely.  This functionality is only available when using k6 locally to trigger tests.

{% highlight js linenos %}
import http from "k6/http";
import moment from "s3.amazonaws.com/k6samples/moment.js";

export default function() {
	http.get("http://test.loadimpact.com/");
	console.log(moment().format());
}
{% endhighlight %}

This also works with modules from hosting services:

{% highlight js linenos %}
import http from "k6/http";
import moment from "cdnjs.com/libraries/moment.js/2.18.1";

export default function() {
	http.get("http://test.loadimpact.com/");
	console.log(moment().format());
}
{% endhighlight %}

## Using Node.js modules

k6 does not run Node.js. However, it can load bundled npm modules with browserify.

Run `browserify` with the [standalone](https://github.com/browserify/browserify-handbook#standalone) option to generate a [Universal Module Definition](http://dontkry.com/posts/code/browserify-and-the-universal-module-definition.html) bundle. This creates a package containing all the dependencies, and make the module available via module.exports. This new package can be imported in a k6 test.

{% highlight js linenos %}

import http from "k6/http";
import { check } from "k6";
import cheerio from "./vendor/cheerio.js";
import xml2js from "./vendor/xml2js.js";


export default function() {

  const res = http.get("https://loadimpact.com/");

  const $ = cheerio.load(res.body);

  const title = $('head title').text();
  check(title, {
    "has correct title": () => title == 'Performance testing for DevOps | LoadImpact'
  });


  var xmlString = '<?xml version="1.0" ?>' +
   	'<items xmlns="http://foo.com">' +
   ' <item>Foo</item>' +
   ' <item color="green">Bar</item>' +
   '</items>'

  xml2js.parseString(xmlString, function (err, result) {
      console.log(JSON.stringify(result));
  });

}

{% endhighlight %}

See the k6 docs on [Node.js](https://docs.k6.io/docs/modules#section-npm-modules) for more information.

## Local modules with Docker

When executing k6 in a Docker container you must make sure to mount the necessary folders from the host into the container, using Docker volumes. This enables k6 to see all the JS modules required.

For example, say you have the following structure on your host machine:

/home/k6/example/src/index.js
/home/k6/example/src/modules/module.js

{% highlight js linenos %}
//myScript.js
import { hello_world } from './modules/module.js';

export default function() {
    hello_world();
}

{% endhighlight %}

{% highlight js linenos %}
// module.js
export function hello_world() {
    console.log("Hello world");
}
{% endhighlight %}

{% highlight bash %}
docker run -v /home/k6/example/src:/src -i loadimpact/k6 run /src/index.js
{% endhighlight %}

## Considerations and Limitations
Each Virtual User will load the required modules into memory during text execution. RAM usage is directly correlated to the number of Virtual Users and the total size of your JS objects. Excessive use of RAM can negatively impact the machine performing the test and the test execution and results.



See Also:
- k6 docs on [module imports](https://docs.k6.io/docs/modules) for additional information.
<!--stackedit_data:
eyJoaXN0b3J5IjpbNDQwMjk1NTQ3XX0=
-->
