---
layout: classic-docs
title: Insights filters
description: Documentation for the Insights filter section
categories: [result-analysis]
order: 2
redirect_from: /knowledgebase/articles/1188154-insights-filters
---

The filter section will help you to:
- Filter results by user tags and system tags.
- Change data aggregation that used in test breakdown structure and analyses panel

You can filter by tags to reduce the result in the breakdown and analysis panel even more . The tags available for filtering are:
- user tags: the once you've added when writing your script.
- system tags: tags automatically assigned as URLs, http method

Read more about [k6 tags](https://docs.k6.io/docs/tags-and-groups).

To see what is available simply click the tag filtering field and you'll be presented with the available tags. If you start typing the list will be narrowed with tags matching the input.

![Insights: Filter tag keys]({{ site.baseurl }}/assets/img/v4/result-analysis/insights-filters-tag-keys.png)

When you see the tag you want click it (or use the TAB-key to autocomplete with the tag highest up in the list). When a tag is selected the list will switch to display available values for that tag instead.

![Insights: Filter tag values]({{ site.baseurl }}/assets/img/v4/result-analysis/insights-filters-tag-values.png)

Selecting a value for the tag works the same way as selecting a tag. Type to narrow the result and click or press TAB to select a value.

![Insights: Filter tag selected]({{ site.baseurl }}/assets/img/v4/result-analysis/insights-filters-tag-selected.png)

You can add several tag filters and they will be visible as tag:value pairs to the right. Click one to remove that filter.

## Aggregation method
Beside the tag filter, you'll find a selector for aggregation method whose options are `mean` and a selection of `percentiles`.

![Insights: Statistics aggregation functions]({{ site.baseurl }}/assets/img/v4/result-analysis/insights-filters-aggregation-stats-funs.png)

When the aggregation method is changed, it will change the result aggregation by including or excluding different data points, and this will affect the visualization in the breakdown and analysis panel.

![Insights: Statistics aggregation function changed]({{ site.baseurl }}/assets/img/v4/result-analysis/insights-filters-aggregation-stats-funs-changed.png)

*Note: this will apply only to metrics type "trend". Read more about [k6 metrics](https://docs.k6.io/docs/result-metrics).*

***

**Next**: [Insights: HTTP Table]({{ site.baseurl }}{% link _v4/result-analysis/insights-http-table.md %})
