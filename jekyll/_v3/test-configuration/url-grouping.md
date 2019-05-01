---
layout: classic-docs
title: URL Grouping
description: How to use LoadImpact's URL Grouping feature
categories: [test-configuration]
order: 8
redirect_from: /knowledgebase/articles/942696-url-grouping
---

***

URL grouping allows you to group several URLs into result. This feature was introduced because one data endpoint can be touched by thousands of similar, slightly varying, URLs. Individually, these unique URLs are not valuable for test results analysis. When grouped together in aggregate, they are much more valuable to the tester.

### Configuring URL groups
You can configure URL groups in your test configuration. A URL group is defined by:

- A display name
- A regular expression pattern, that is used to match URLs that should be part of a grouping.

![URL Grouping examples]({{ site.baseurl }}/assets/img/v3/test-configuration/url-grouping/url-grouping-1.png)


The order of the URL groups is important: if a given URL matches multiple regular expressions it will be assigned to the first group in the list. You can reorder groups by clicking arrow buttons on the left side of the table.

![Creating a URL grouping]({{ site.baseurl }}/assets/img/v3/test-configuration/url-grouping/url-grouping-2.png)

**Note**: Don't forget to save your test configuration after updating the URL group list!

### URL grouping in test results

During the test execution all urls from the test script that match the given regular expression of a URL group will be combined and displayed as a single entry in the “URLs” overview section of the test results page. This enables you to optimize your test results, making them easier to analyze by reducing the number of URLs without losing the relevant information.

![URL Grouping in a test]({{ site.baseurl }}/assets/img/v3/test-configuration/url-grouping/url-grouping-3.png)
