---
layout: classic-docs
title: Insights test comparison
description: Documentation for the test comparison functionality in Insights
categories: [result-analysis]
order: 6
---

***

<h1>Background</h1>

The test comparison feature built-in to Insights allows you to compare the results of two different test runs of the same test. You can compare high-level metrics and individual checks and URL endpoints. You may wish to compare against a previous test run to look for differences in results. Or you may be comparing against a known baseline.

Comparing results against a known [baseline]({{ site.baseurl }}{% link _v4/guides/general-methodology.md %}#phase-2---baseline-testing-scaling-your-tests-and-complex-cases) is a core part of the General Methodology for [performance testing]({{ site.baseurl }}{% link _v4/guides/general-methodology.md %}). Baseline tests are important as they allow you to compare against a control to look for differences. Baseline tests should produce enough load to contain meaningful data and ideal results.  In other words, a heavy stress test isn't a good baseline. Think much smaller.

## Selecting test run to compare against

To compare two test runs, open up one of the test runs. Then select the test run you want to compare it to using the select widget on the right side of the test result page, just above the [Performance overview]({% link _v4/result-analysis/insights-overview.md %}) section. Note that you can only compare against other test runs of the same test.

![Insights: Select test run for comparison]({{ site.baseurl }}/assets/img/v4/result-analysis/test-comparison/test-comparison-select-test-run.png)

## Performance overview comparison

When in test comparison mode the performance overview section will show the overview chart for both test runs. This gives you a quick way to see if there are any performance differences on a high level.

![Insights: Performance overview comparison]({{ site.baseurl }}/assets/img/v4/result-analysis/test-comparison/test-comparison-perf-overview.png)

## Thresholds tab comparison

In the thresholds tab you'll see an additional "Diff" column being added to the table. This column will show the current vs compared test run's pass/fail status for each threshold. You'll also see a separate threshold charts for each test run.

![Insights: Thresholds comparison]({{ site.baseurl }}/assets/img/v4/result-analysis/test-comparison/test-comparison-thresholds.png)

## Checks tab comparison

In the checks tab you'll see an additional "Difference in failure %" column being added to the table. This column will show the difference in failure rate between the current and compared test runs. You'll also see a separate checks charts for each test run.

![Insights: Checks comparison]({{ site.baseurl }}/assets/img/v4/result-analysis/test-comparison/test-comparison-checks.png)

## HTTP tab comparison

As in the Thresholds and Checks tab, you'll see a new column in the table. This time the you'll have a select box in the title where you can select based on what statistic you want to compare. It defaults to 95th percentile, but you also have the option to select min, avg, 99th percentile and max.

When you've made a selection you'll also notice that the content of the corresponding statistic column will change to show the current vs compared test run value for that statistic. In the screenshot below you'll see it in the 95th percentile column.

Clicking on a row will also show two separate charts, one for each test run.

![Insights: HTTP comparison]({{ site.baseurl }}/assets/img/v4/result-analysis/test-comparison/test-comparison-http.png)

## Analysis tab comparison

When you use the "Add this graph to analysis tab" action in the other tabs, two charts will be added to the analysis panel, one for each test run. Same goes if you add a metric via the "Add metric to visualize" button, you'll get two charts.

<div class="callout callout-warning" role="alert">
    <b>Only metrics from current test run can be added to comparison chart</b><br>
    At this point metrics from the compared test run can't be added to the comparison chart.
</div>

![Insights: Analysis tab comparison]({{ site.baseurl }}/assets/img/v4/result-analysis/test-comparison/test-comparison-analysis.png)
