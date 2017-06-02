---
author: mayertbryan
comments: true
date: 2017-03-13 
layout: post
slug: robust-estimator-decreases-se
title: robust-estimator-decreases-se
wordpress_id: 4
---

**Does robust estimation of the standard error (i.e., the sandwich estimator) always decrease your precision?** (Short answer: No)

Consider these two poorly designed and poorly funded studies trying to find the population mean blood pressure. 

Study 1: 8 people are measured two measurements of blood pressure
Study 2: 16 people with one measurement of blood pressure. 

One might approach this problem similarly in both studies: just take the average and plop on some CIs. But we hopefully know that we probably shouldn't treat 16 measurements from 8 people the same way as 16 measurements from 16 people. If we could do this, why not just take 16 measurements from 1 person. Running a study is easy! 

Multiple measurements on a single person might give some information on population variability, but there is likely a limit and so you want more people in a study. I don't think this is necessarily obvious though, and I have attended many academic talks where repeated measures are treated (or at least talked about) independently. Most commonly when sequencing in involved, and patients and data are hard to come by. Unfortnately, while you maybe able to draw a beautful and potentially precise phylogenetic tree for HIV in a single person, you still only have a single person for a population inference. 

So what is my point here. Well in study 1, we ultimately only have 8 people. The second measurement for each person helps us with the individual-level precision (I'm more confident in my outcome for a given person) but it came at a cost: the population-level estimate will be less precise because I have less people. I like to think of this as having some "effective sample size", it is not as bad as 8 but not as good as 16.

This is where (robust error estimation)[https://en.wikipedia.org/wiki/Heteroscedasticity-consistent_standard_errors] comes in. Without considering the mechanics at all, this nifty little method is a correction to handle these scenarios. In this case, it should give an estimate consistent with my "effective sample size" idea, that is a larger standard error than if we treated the data independently.

But wait! According to my opening question this is not always the case. So there must be an example where we get better precision from Study 1 using the robust estimator. Well we can't make up the sample size, so it must be made up by utilizing the covariance in some way.


Recently, I "well actually"ed somebody on this issue, and was not prepared for their response: "ok, so what's an example?". Naturally I was immediately filled with self-doubt. If you google this issue, you will eventually be pointed to a (comment thread)[http://www.mostlyharmlesseconometrics.com/2010/12/heteroskedasticity-and-standard-errors-big-and-small/] which leads to this (nifty little proof)[http://econ.lse.ac.uk/staff/spischke/mhe/josh/Notes%20on%20conv%20std%20error.pdf].

So if your residuals are negatively correlated with the deviation of the independent variable from the average, your se will be too large. I tried to unpackage that a little and devise an example but I misunderstood the proof and devised a different example. BUT that example also happened to have lower robust errors than the unadjusted errors.



To start, I'm using 'robust estimation' pretty loosely, but let's say that it is the classic 'sandwich estimator.'

The answer is no, but the conditions for this may not be intuitive. In fact, most time someone talks about using robust error correction, . 

