---
layout: classic-docs
title: Data Parameterization
description: Code samples on how to parameterize data in your load test.
categories: [examples]
order: 4
---

***

<h2>Background</h2>

Scripting examples on how to parameterize data in a test script. Parameterization is typically necessary when Virtual Users(VUs) will make a POST, PUT, or PATCH request in a test.  Parameterization helps to prevent server side caching from impacting your load test.  This will make your test more realistic.

***

## Data files/Parameterization

### Reading parameterization data from a CSV file
As k6 doesn't support parsing CSV files out of the box, we'll have to resort to using a Node.js library called [Papa Parse](https://www.papaparse.com/) and [Browserify](http://browserify.org/) (to make it work in k6).

There are a few of steps to make this work:

1. Make sure you have the necessary prerequisites installed:
    * [Node.js](https://nodejs.org/en/download/)
    * [Browserify](http://browserify.org/)
2. Install the `Papa Parse` library:

    <kbd>npm install papaparse</kbd>
3. Run it through browserify:

    <kbd>browserify node_modules/papaparse/papaparse.min.js -s papaparse > papaparse.js</kbd>
4. Move the `papaparse.js` file to the same folder as your script file and you'll be able to import it into your test script:

    `import papaparse from "./papaparse.js"`

Here's an example using Papa Parse to parse a CSV file of username/password pairs and using that data to login to the LoadImpact test site:

{% include 4.0/scripting-examples/data-parameterization-csv.md %}

### Reading parameterization data from a JSON file
{% include 4.0/scripting-examples/data-parameterization-json.md %}
