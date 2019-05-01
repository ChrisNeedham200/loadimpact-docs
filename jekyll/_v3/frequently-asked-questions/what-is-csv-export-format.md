---
layout: classic-docs
title: What is the CSV export format?
description: Examples and definition of LoadImpacts CSV export format
categories: [frequently-asked-questions]
order: 8
redirect_from: /knowledgebase/articles/174526-will-load-testing-transactions-show-up-on-google-a
---

***

Test results data can be exported as CSV files (comma-separated values), for easy importing into e.g. Excel or other spreadsheet applications. Below is a description of the CSV format. Let’s start with a couple of examples.

#### Example 1
a line logging the current number of active clients (2) that on the Ashburn load zone:

`2012-06-30 09:57:41.084033,__li_clients_active,,"Amazon US East (Ashburn, US)",,,,,,,2.0,,,,,,,,,,`

#### Example 2

a line logging min/max/avg transaction times (105.88ms, 182.17ms and 139.76ms, respectively) and number of transactions (12) for the URL “www.domain.com/image1.jpg” retrieved using the HTTP method “GET” by clients running the user scenario “Websurfer #1” on the Ashburn load zone and where the HTTP status (response) code for the transactions was 200:

`2012-06-30 09:57:25.843394,__li_url_0d44baa2141a04aa04f92269e0c6e52d,http://www.domain.com/image1.jpg,"Amazon US East (Ashburn, US)",Websurfer #1 (ID: 1138842),200,105.88,182.17,139.76,12,,GET,image/png=1,0,1247.0,0.0,,http://loadimpact.com/static/images/logos/xjwt.png.pagespeed.ic._oK2TgeEga.png,,,`

The URL itself is included twice in the output. This is for 3.0 reasons and will most likely change in the future.

Detailed information about the CSV fields
All lines use the following column format (fields):

`Time,Result ID,URL,Load zone,User scenario,Status code,RTT min,RTT max,RTT avg,Count,Value,Method,Content types,Compressed count,Avg content length,Avg compressed content length,Content type,Custom metric/Page title,Custom metric type,Log severity level,Log message,Standard deviation,Median`

Column name|Description|Example
Time|UTC timestamp when the result was recorded. In ISO-8601 format (YYYY-MM-DD HH:MM:SS)|	2011-11-22 16:12:17
Result ID|Unique name for the result. For automatically generated metrics, it will start with “li_”	|__li_requests_per_second
URL|What URL the result value relates to, if the result is a URLresult (has a __Result ID that starts with “li_url_”), otherwise this field will be empty|	http://www.dom.ain/jquery.validate.js
Load zone|The name of the load zone where the result was collected|	"Amazon US East (Ashburn, US)"
User scenario|The name (and ID number) of the user scenario that collected the result	| My User Scenario name (ID: 1005974)
Status code | What HTTP status code the result value relates to, if the result is a “__li_url_” result, otherwise this field will be empty	|200
RTT min|The minimum transaction time (in milliseconds if the Custom metric type value is not present) seen for the URL since last report. This field is empty if the result not a “__li_url_” result	|132.74
RTT max|The maximum transaction time (in milliseconds if the Custom metric type value is not present) seen for the URL since last report. This field is empty if the result not a “__li_url_” result|	2447.61
RTT avg|The average transaction time (in milliseconds if the Custom metric type value is not present) seen for the URL since last report. This field is empty if the result not a “__li_url_” result|	159.34
Count|The number of original transactions that were used to compute this aggregated result. I.e. in the case of reporting transaction times for status code 200 for URL __X, the Count value shows how many such transactions occurred during the report interval	|93
Value|This is the actual measurement value for all results that do not begin with “__li_url_” (where transaction time reporting is usually not applicable)|	12345
Method|The HTTP method used in the transaction, if applicable|	GET
Content types|A list of the content types seen, and how many times each type was encountered during the test. Only applicable for __li_content_type and __li_url_ results	|image/png=21\|\|text/css=2\|\|text/javascript=3
Compressed count|How many times the resource was compressed. Only applicable for URL type result metrics (i.e. with result IDs starting with __li_url_)|	0
Avg content length|Average size of returned content (after decompression, if it was compressed). Only applicable for URL type result metrics (i.e. with result IDs starting with __li_url_)	|6002.0
Avg compressed content length|Average size of compressed, returned content (i.e. before decompression). Only applicable for URL type result metrics (i.e. with result IDs starting with__li_url_)|	0.0
Content type|Resource content type, according to the HTTP “Content-Type” header. Only used by the __li_content_type_load_time result metric|	image/png
Custom metric/page title|Name of the custom metric or the page title (metrics collected using the http.page_start() and http.page_end() API functions are actually custom metrics). Ordinary URL metrics will currently also use this field, which will then contain the URL|	Page 1
Custom metric type|The unit of measurement for the custom metric. This will be used when plotting graphs for the metric. Only applicable to custom metrics|	seconds
Log severity level|The severity level used when logging text messages with the log.* functions. Logging with log.info() results in severity level “info”. Similarly, logging with log.error() results in severity level “error” and log.debug() becomes severity level “debug”. This field is only applicable to log messages (i.e. __li_log result IDs)|	info
Log message|The text message that was logged by a simulated client. This field is only applicable to log messages (i.e. __li_log result IDs)|	Hello world
Standard deviation|The standard deviation calculated by the server metrics agent, if used in the test.|	0
Median|The median calculated by the server metrics agent, if used in the test.|	7.3
{: class="table table-bordered"}

Note some string values have comma characters in them. Like “Amazon US East (Ashburn, US)”. These strings will be enclosed in quotation marks (”“) to make the string be parsed as one single field value, despite containing the separator character (comma).
