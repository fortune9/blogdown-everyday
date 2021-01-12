---
title: Add office 365 calendar to Thunderbird
author: Zhenguo Zhang
date: '2021-01-11'
slug: add-office-365-calendar-to-thunderbird
categories:
  - Tips
tags:
  - Windows
---

Thunderbird is a powerful software to handle emails and calendar
accounts. Recently, I had a new laptop and installed the latest
Thunderbird version 78.6.0, and want to add my office 365 calender
into it.
).

## Steps

After searching internet for a while, I found that the following
solution worked out:

1. Install [TbSync](https://addons.thunderbird.net/en-US/thunderbird/addon/tbsync/) addon.

2. Install [Provide for Exchange ActiveSync](https://addons.thunderbird.net/en-US/thunderbird/addon/eas-4-tbsync/) addon.

3. Start the TbSync Addon from *Tools*->*Addon options*->*TbSync*.

4. Then choose *Account actions*->*add new account*->*Exchange ActiveSync*.

5. In the opened window, choose *Automatic configuration* and fill
the username(email address) and password, and click 
*Autodiscover ...* button.

6. If everything goes well, you'll see a list of available calendars
and tasks from the server, and check those you'd like to
synchronize to Thunderbird. Also set the time interval
for periodic checking remote server.

Then it is done.

Note: I tried the *Microsoft Office 365* option, and it didn't
work out.

## Reference

1. https://www.systutorials.com/how-to-synchronize-thunderbird-calendar-and-address-book-with-office365-exchange-online-using-activesync/
