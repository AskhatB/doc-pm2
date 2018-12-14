---
layout: page
title: Notifications | Guide | PM2 Plus Documentation
menu: starter
lang: en
section: plus
permalink: "/en/plus/guide/notifications/"
---

# Notifications

Notifications are one of the most powerful feature of PM2 Plus, always getting you aware about critical events.

By default, you receive notifications only by email and for critical events such as:
- downtime
- deployment
- issues

This section will help you to customize the notification systems, to setup other channels and specify your own kind of alerts through your code.

## Default notifications

By default, PM2 Plus sends three different kind of notification.

- When a new exception is thrown.

[New Exception] Exception detected on *process_name*

- When a server is offline for more than 90 seconds

- After a deployment

[App Malfunctioning] Application throws too many errors (*process_name*)

## Custom notifications

### Custom metric notifications

The main way to set custom notification with PM2 Plus is with custom metrics.

When defining a custom metric, enable notifications on it if it reaches a specific threshold.

Example :

```javascript
const metric = probe.metric({
  name: 'CPU usage',
  value: () => {
    return cpu_usage;
  },
  alert: {
    mode: 'threshold',
    value: 95,
    msg: 'Detected over 95% CPU usage', // optional
    action: () => { //optional
      console.error('Detected over 95% CPU usage');
    },
    cmp: (value, threshold) => { //optional
      return (parseFloat(value) > threshold); // default check
    }
  }
});
```

Available options are:

- mode:
  - threshold: trigger an alert directly when the value is above or below the threshold
  - threshold-avg: trigger an alert when the value is above or below the threshold for *X* seconds
  - smart: trigger an alert automatically when the value is unusual
- value: Value that will be used for the exception check.
- msg: String used for the exception.
- action: Function triggered when the exception is reached. Optional.
- cmp: Function used for exception check taking 2 arguments. Optional.
- interval: For the threshold-avg mode. Sample length for monitored value (180 seconds default). Optional.
- timeout: For the threshold-avg mode. Time after which mean comparison starts (30 000 milliseconds default). Optional.

### Custom event notifications

An other way to set a custom notifications is to use a custom event.

After having defined a custom event, subscribe to it directly in the dashboard.

## Notification channels

By default, notifications are only sent by email but they can be enabled with slack or via a webhook.

### Slack notifications

The Slack integration allows you to receive exceptions and event notifications straight into a selected Slack channel.

First you need to get the Slack URL and to setup an incoming Webhook. More details on how to set this up can be found [here](https://my.slack.com/services/new/incoming-webhook/).

Then go to the notification page and insert the webhook into the field. Enable and click on update.

Check if you successfully received a notification into your slack channel confirming that it has been configured.

### Webhooks

You can also set a webhook that will make POST HTTP request to a given URL when you receive a notifications.

The format of the data is a json like the following:

```json
 {
   "event":"event:new_exception",
   "data":{
      "process":{
         "pm_id":9,
         "name":"pm2-elasticsearch",
         "rev":"ac77098c5e1b10d74360b113da6e717fab8fe427",
         "server":"pm2-module-testing"
      },
      "data":{
         "message":"Bad argument",
         "stack":"TypeError: Bad argument\n    at TypeError (native)\n    at ChildProcess.spawn (internal/child_process.js:274:26)\n    at exports.spawn (child_process.js:362:9)\n    at Object.exports.execFile (child_process.js:151:15)\n    at exports.exec (child_process.js:111:18)\n    at /home/node/pm2-elasticsearch/lib/actions.js:25:5\n    at process.<anonymous> (/home/node/pm2-elasticsearch/node_modules/pmx/lib/actions.js:64:14)\n    at emitTwo (events.js:92:20)\n    at process.emit (events.js:172:7)\n    at handleMessage (internal/child_process.js:695:10)"
      },
      "at":1472651928925,
      "created_at":1472651928925,
      "updated_at":1472651928925,
      "count":1,
      "identifier":"ad6f8650dabfec83f183633b0bba7d97",
      "infected_servers":[
         "pm2-module-testing"
      ],
      "timestamps":[
         1472651928925
      ],
      "commits":[
         "ac77098c5e1b10d74360b113da6e717fab8fe427"
      ],
      "bucket_url":"https://app.pm2.io/.../"
   }
}
```
 
Use case example: You can now setup an express server that can receive webhooks, automatically send SMS or use any integration you want.

## Next Steps

[Issue Dashboard]({{ site.baseurl }}{% link en/plus/guide/issue-dashboard.md %})
{: .btn-stylized}
