---
layout:     post
title:      Monitoring my website and home downtime

---

I was searching for a way to monitor the uptime of my website and alert me if there was any significant downtime. There are solutions like Prometheus which provide highly configurable and scalable alerting and monitoring, but I don't need something so complex right now. I also looked at uptimerobot, but didn't like the features it provided, and configuration seemed clunky.

I settled on a service called updown.io. It polls websites or IP targets and measures uptime statistics.

EXAMPLE PHOTO

# Configuring Monitoring

Monitoring is simple, enter the URL of the site you want to monitor, optionally add keywords to look for in a request,

# Setting up Alerts

Alerts
Slack, webhooks, SMS

# Integration with my Alerting
