---
author: mayertbryan
comments: true
date: 2013-12-18 22:17:00+00:00
layout: post
slug: nesting-rbind-merge-ddply
title: nesting rbind/merge/ddply
wordpress_id: 13
---

In this I need to extract baseline and final values from a time series for one set of bacteria based on the analysis of another set. The final time varies by person and is derived in the separate analysis so that must be extracted individually then I must use this time to find the final value. In the end I want to combine the information from that separate analysis with the values I extracted. This is the ugliest code I've ever written.

