---
title: How to disable Youtube shorts when uploading videos?
author: Zhenguo Zhang
date: '2022-05-26'
slug: how-to-disable-youtube-shorts-when-uploading-videos
categories:
  - Tips
tags:
  - Youtube
  - video
description: ''
featured_image: ''
images: []
comment: yes
---

Recently, I need share a few videos via Youtube channel.
However, the receiver said that the video continuously
replayed itself.

## The problem

After some research, I noticed that the uploaded videos
are short in length (less than 1 min), so these videos
are marked as [Youtube shorts video](https://www.youtube.com/hashtag/shorts), and
they automatically replay all the time.

Here is an example of short video with the URL
https://www.youtube.com/shorts/nq-epMpa0E8

## The fix

As you can see in the URL, it has the format
https://www.youtube.com/**shorts**/*video-id*.
The sub-folder **shorts** indicates the video
is from Youtube shorts.

To disable this, one can change the URL to
one of the following formats:

* http://youtu.be/*video-id*, like https://youtu.be/nq-epMpa0E8
* https://www.youtube.com/watch?v=*video-id*, like https://www.youtube.com/watch?v=nq-epMpa0E8

Then these videos will be played as regular
Youtube videos, rather than Shorts.

:smile:

