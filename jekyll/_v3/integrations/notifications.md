---
layout: classic-docs
title: LoadImpact Notifications
description: Notifications allow you to send updates to be aware of tests starting and finishing. You can view these notifications in Slack, Hipchat, or via webhook.
categories: [integrations]
order: 3
redirect_from: /knowledgebase/articles/878235-webhooks
---

***

Notifications allow you to subscribe to events happening in your organizations/projects. You can receive notifications to be aware when:
- A test has started.
- A test has completed.


You will likely want to use notifications when you schedule your performance tests and/or configure them into your Continuous Integration pipeline.

LoadImpact supports three different notification options:
- Slack
- HipChat
- WebHook

**Note:** Notifications are configured per organization by the organization owner or an admin member.

![LoadImpact Notifications]({{ site.baseurl }}/assets/img/v3/integrations/notifications/loadimpact-notifications.png)


### Slack
Slack is a messaging app for teams. Follow these instructions to configure Slack notifications:

1. From Slack, add a new app and select Incoming WebHook app.
2. Select or create a channel and copy the generated WebHook URL.
3. From LoadImpact, select Notifications from your organization settings. (User menu > Organizations > Organization setting > Notifications)
4. Add Slack WebHook URL into the Slack input and click Save or Test Hook.

![Slack Setup]({{ site.baseurl }}/assets/img/v3/integrations/notifications/slack-instructions.png)

***

### HipChat
HipChat is a group chat & IM for teams . Follow these instructions to configure HipChat notifications:

1. From HipChat, go to Integrations, select a Room and the “Build your own integration” option from the list of integrations. Copy the room notification URL presented in the "Send messages to this room by posting this URL" field on the next page.
2. From LoadImpact, select Notifications from your organization settings. (User menu > Organizations > Organization setting > Notifications)
3. Paste room notification URL into the HipChat input and click Save or Test Hook.

![Hipchat Setup]({{ site.baseurl }}/assets/img/v3/integrations/notifications/hipchat-instructions.png)

***

### WebHooks

When an event is triggered, we'll send a HTTP POST request to the configured URL with a JSON payload containing event specific data. The format is explained in the following section.

***

#### Notification events

Headers sent with all requests

Header             | Description
-------------------|--------------------------------------------------------------------
X-LoadImpact-ID    | Unique ID for this request
X-LoadImpact-Event | Name of the event
User-Agent         | User agent for webhook requests always start with `LoadImpactWebHook`
{: class="table table-bordered"}

```
    Example headers:
    X-LoadImpact-ID: 19c5d426-3b4d-43c3-8277-37ad7d457430
    X-LoadImpact-Event: test.started
    User-Agent: LoadImpactWebHook
```
##### Load test run started event

Sent when a load test is starting.

Example JSON body:
```
{
    "status": 2,
    "status_text": "Running",
    "user_id": 1,
    "name": "Load test",
    "organization_id": 1,
    "load_test_id": 1,
    "load_test_run_id": 1,
    "project_id": 1,
    "event": "test.started"
}
```

##### Load test run finished event

Sent when a load test finishes, aborts or fails

Example JSON body:
```
{
    "status": 3,
    "status_text": "Finished",
    "user_id": 1,
    "name": "Load test",
    "organization_id": 1,
    "load_test_id": 1,
    "load_test_run_id": 1,
    "project_id": 1,
    "event": "test.finished"
}
```
##### Status Codes

Status | Description
-------|-------------------------------
-1     | Created
0      | Queued
1      | Initializing
2      | Running
3      | Finished
4      | Timed out (currently not used)
5      | Aborting by user
6      | Aborted by user
7      | Aborting by system
8      | Aborted by system
9      | Aborted by script error
10     | Aborting by threshold
11     | Aborted by threshold
12     | Failed threshold
{: class="table table-bordered"}

***

#### Test notification channel event

Test event that can be triggered from the UI to test webhook

Example JSON body:
{
    "event": "test.notification_channel"
}
