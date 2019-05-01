---
layout: classic-docs
title: Generating UUIDs
description: Code sample on how to generated UUIDs in your load test
categories: [examples]
order: 9
---

***

<h2>Background</h2>

Scripting example on how to generate UUIDs in your load test.

***


## UUID

### Generate v1 and v4 UUIDs
Universally unique identifier are handy in many scenarios, as k6 doesn't have built-in support for UUIDs we'll have to resort to using a Node.js library called [uuid](https://www.npmjs.com/package/uuid) and [Browserify](http://browserify.org/) (to make it work in k6).

There are a few of steps to make this work:

1. Make sure you have the necessary prerequisites installed:
    * [Node.js](https://nodejs.org/en/download/)
    * [Browserify](http://browserify.org/)
2. Install the `uuid` library:

    <kbd>npm install uuid</kbd>
3. Run it through browserify:

    <kbd>browserify node_modules/uuid/index.js -s uuid > uuid.js</kbd>
4. Move the `uuid.js` file to the same folder as your script file and you'll be able to import it into your test script:

    `import uuid from "./uuid.js"`

Here's an example generating a v1 and v4 UUID:

{% include 4.0/scripting-examples/uuid-generate-v1-v4.md %}
