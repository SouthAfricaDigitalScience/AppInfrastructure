---
layout: post
title: "Introducting the App <-> Infrastructure blog"					# Title of the post
description: Some description		# Description of the post, used for Facebook Opengraph & Twitter
headline: Some headline				# Will appear in bold letters on top of the post
modified: YYYY-MM-DD				# Date
category: personal
tags: []
image: 
  feature: 
  comments: true
mathjax:
---

This is the first post in the App <-> Infrastructure blog. Here, the idea is to write about the lessons, successes and of course frustrations that we find between Joe User and BOFH. It's a space that's often written about, and is held up as the so-called "3rd career path", sitting between the scientist and the IT expert or site administrator. Most of the SAGrid Ops team sits in this area from time to time, directly working with the research groups or individual scientists to make their code **work**.

That is the main point : it's alright to have an amazing set of tools and services on the grid *in principle*, but if at the end of the day this actually represents a barrier to entry, there's a problem.

## The grid : it's *complicated* ! No - *complex* (and that's a good thing)
One of the impressions that I'd like to dispel with this blog is that using the grid requires some kind of mutant super power... (or on the other hand requires you to be physicist; argh)
Perhaps it's the interfaces that people are expecting to be more intuitive, perhaps it's that some people are stuck in their ways of doing things; perhaps it's that most people don't realise the potential, or have a hard come appreciating the features. Everyone is different, and most time, people expect things to be done "their way", which is obviously the most logical way of doing things. Anything else would be a bit like the guy in the viral vine using someone else's shower.

<object class="BLOGGER-youtube-video" classid="clsid:D27CDB6E-AE6D-11cf-96B8-444553540000" codebase="http://download.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=6,0,40,0" data-thumbnail-src="http://img.youtube.com/vi/kO13st_GIoM/0.jpg" height="266" width="320"><param name="movie" value="http://youtube.googleapis.com/v/kO13st_GIoM&source=uds" /><param name="bgcolor" value="#FFFFFF" /><param name="allowFullScreen" value="true" /><embed width="320" height="266"  src="http://youtube.googleapis.com/v/kO13st_GIoM&source=uds" type="application/x-shockwave-flash" allowfullscreen="true"></embed></object><br />

It's true that using the grid does impose certain constraints on the way one does things - and it does so in the very first step, when people ask "where do I log in, what's my user name", and you have to take a step back and give them the long talk about PKI, personal certificates and proxies. While this is of course more secure than the ol' username/password it's a barrier to entry and not a good way to start the conversation with someone you're trying to persuade to invest their time and effort in using the services.

One thing's for sure : when it comes to doing <i style="font-weight: bold;">complex</i>&nbsp;things, the grid is your friend. Yes, it's easy to share a file with <a href="https://www.dropbox.com/">Dropbox</a> (as long as it's not too big). Yes, it's easy to use MATLAB pre-installed on a cluster - if that's all you're doing. But what about the case where you need to use a set of different applications; what about the case where you need to <b>move</b>&nbsp;data (not necessarily share it) ?

We'll continue to show how using a complex service-oriented infrastructure beats ad-hoc, vertical solutions in this blog. If we make it through lunch...
