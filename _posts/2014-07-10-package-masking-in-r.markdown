---
author: mayertbryan
comments: true
date: 2014-07-10 04:22:00+00:00
layout: post
slug: package-masking-in-r
title: package masking in R
wordpress_id: 7
---

Sometimes dplyr and plyr mask eachother's functions. Easy solution is to utilize the "::" operator.  (The best solution is to
only load dplyr and call all plyr functions using plyr::.)  


    summaryData = myData %>% group_by(some_category) %>% 
    dplyr::summarise(new_mean = mean(some_variable)) 
