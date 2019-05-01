---
layout: classic-docs
title: Filling and Submitting HTML Forms
description: Code samples on how to fill and submit HTML forms in your load test
categories: [examples]
order: 5
---

***

<h2>Background</h2>

Scripting example on how to handle HTML forms in a load test.

***

## HTML

### Filling in and submitting forms
One of the most tedious tasks when testing websites and apps is to get all the form filling to work. You have to get all the so called "correlations" ([see above](#correlation)) correct which can take time, even with the help of a scenario recorder as the staring point for getting the basic user journey down into a re-playable test.

{% include 4.0/scripting-examples/html-fill-submit-form.md %}

**Relevant k6 APIs**:
- [Response.submitForm([params])](https://docs.k6.io/docs/responsesubmitform-params)
- [Selection.find(selector)](https://docs.k6.io/docs/responsesubmitform-params) (the [jQuery Selector API](http://api.jquery.com/category/selectors/) docs are also a good resource on what possible selector queryies can be made)
