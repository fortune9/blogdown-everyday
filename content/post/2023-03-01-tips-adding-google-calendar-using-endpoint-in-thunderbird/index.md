---
title: '[Tips] Adding google calendar using endpoint in Thunderbird'
author: Zhenguo Zhang
date: '2023-03-01'
slug: tips-adding-google-calendar-using-endpoint-in-thunderbird
categories:
  - Tips
  - Software
tags:
  - Thunderbird
  - Google calendar
description: ''
featured_image: ''
images: []
comment: yes
---

I have used Thunderbird as my daily
email and calendar tools for decades for its expandable
features via addons. But sometimes, it suffers bugs,
just like recently, it stops synchronization with google
calender after I updated to version 102.8.0. This bug
triggers me to explore potential solutions, and it leads
to today's topic -- adding google calendar via endpoint.

## Three methods to add google calendar

When one wants to add google calendar to Thunderbird,
the popup window shows three methods:

- On my computer
- On the network
- Google calendar (need the addon [Provider for Google Calendar](https://addons.thunderbird.net/thunderbird/addon/provider-for-google-calendar/))

Normally, I use the 3rd option to add google calendars,
but it failed recently. After some research, I found the
second option is viable for google calendar. The steps
are described in next section.

## Add google calendar using endpoint

So one chooses the option 'On the network', and click
next, on the next window, one need input the following
information, as displayed in the image:

![](/post/images/google-calendar-endpoint-setting.png)

- **Username**: input your google email address
- **Location**: input the endpoint URL https://apidata.googleusercontent.com/caldav/v2/calid/events

Then click the "Find Calendars" button, and the next page
will asks your google account password, followed by
choosing what calendars to synchronize to Thunderbird.
That's it.

Note that not all methods work at the same time,
and this is why I write this blog so that I can refer to
it in the future when needed. I hope that this is
helpful to you, too.

## References

1. https://support.mozilla.org/en-US/questions/1300521#answer-1343279

