---
layout:     post
title:      Receiving Alerts using IFTTT Webhooks

---
I like to use [IFTTT](https://ifttt.com) webhooks to receive notifications from various services on my phone. Normally services can publish alerts and notifications to a webhook, which [IFTTT Webhooks](https://ifttt.com/maker_webhooks) supports. However, you have to format the data in the following way:
```JSON
{ "value1" : "STUFF", "value2" : "STUFF", "value3" : "STUFF" }
```
You can then use these values in whatever action service you want in IFTTT.
But what if the service publishing to the IFTTT webhook doesn't support the above format? My approach depends on the service in question. Generally, for notifications from AWS I use the Simple Notification Service and a custom Lambda function. For other services, I use automate.io. Both of these methods let me format the data for IFTTT webhooks.

## AWS SNS and Lambda
 AWS SNS is a pub/sub service that allows for publishing to a topic and subscribing to that topic. As far as I know, all AWS services support publishing to an AWS SNS topic in some way. This makes it extremely powerful for receiving notifications about your AWS infrastructure.

 However, it's not so simple to subscribe IFTTT to a notification topic. It's true SNS supports native publishing to HTTP/HTTPS endpoints, but not in the format required by IFTTT. This is where AWS Lambda comes in. Using a bit of code found [here on GitHub](https://github.com/danilop/SNS2IFTTT/), we can set up a Lambda function that subscribes to a SNS topic, and relays that information to IFTTT.

 First, set we set up an SNS topic. Then create a Lambda function using the NodeJS runtime. Let AWS create a service role for you, and set up the trigger to be the SNS topic created earlier.
![Lambda SNS Trigger](/assets/lambda2.png)

 In testing with the bit of code above, I noticed the credentials were hardcoded. This is a huge no-no, so I opted to modify it to use environment variables. The code with my modifications can be found [here](https://github.com/twaluigi/sns2ifttt). Here's the relevant bit using environment variables.
 ```NodeJS
 // IFTTT Maker Webhooks configuration, see https://ifttt.com/maker_webhooks
var iftttMakerEventName = process.env.iftttMakerEventName;
var iftttMakerSecretKey = process.env.iftttMakerSecretKey;

var iftttMakerUrl =
    'https://maker.ifttt.com/trigger/'
    + iftttMakerEventName
    + '/with/key/'
+ iftttMakerSecretKey;
```

Head over to IFTTT and create an applet with the trigger as the maker webhooks service. If you want notifications sent to your phone, set up the applet like so
![IFTTT applet setup](/assets/ifttt1.png)

Once set up, you'll find the information to enter in for the MakerEventName and MakerSecretKey. Use those values in Lambda as environment variables as shown below.

If all went as planned, you should be able to publish to the SNS topic and receive a notification on your phone. I much prefer this than receiving a text every time a service starts failing, or even worse, an email.
![IFTTT phone notification](/assets/ifttt2.png)

## Automate.io
For other services, I've opted to use [Automate.io](https://automate.io/) to act as the intermediary between services and IFTTT. For instance, I use [updown.io](https://updown.io) to monitor my website uptime and home internet reliability. They support publishing to a webhook, but again, not in the format required by IFTTT. This is where automate.io comes in. It acts as a go-between for whatever service needs to publish to a webhook and IFTTT.

For updown.io, I have two apps, or "Bots" set up to be triggered if a website goes up or down. Automate.io grabs the output json from the updown.io notification. It filters it based on if the site has gone up or down to customize the notification I get on my phone. Then, it puts relevant data into JSON format for IFTTT, and POSTs to the IFTTT webhook.
![automate io bot setup](/assets/automate1.png)

It's very helpful to get notified about changes to my infrastructure, and IFTTT makes it easy to do so.
