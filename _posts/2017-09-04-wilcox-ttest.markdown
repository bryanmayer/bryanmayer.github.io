---
author: mayertbryan
comments: true
date: 2017-09-04 21:10:48+00:00
categories: rblogging
layout: post
slug: wilcox-ttest
title: Power loss using Wilcox
wordpress_id: 4
---


I know this is an answered question, but I was curious how detrimental it is to use a Wilcoxon rank-sum test in lieu of a student t-test. This brief simulation assumes that the assumptions for a simple two-sample t-test are met:

1.  The data are a simple, random sample.
2.  The data are on an interval scale (i.e., continuous without boundaries).
3.  The populations have equal variance.
4.  The populations are normally distribution.

A little aside, does assumption 4 matter? Proofs of the t-test seem to always start with the assumption that our data are normally distributed (for example, see Casella & Berger). However, the proof that the t-statistic is t-distributed only requires that the sample mean is normally distributed (also covered in Casella & Berger). This is guaranteed by assuming our data are normal but is also a result of the CLT assuming we have 1.) a behaved distribution (i.e., basically most continuous distributions that we can easily sample in R), and 2) a large enough sample size. Long story short, I'm not concerning myself with non-normal data right now, but it could certainly be an issue for small samples of skewed distributions.

Here is a straightforward function that takes sample size (`n`), a difference in means (`mean_difference`) and standard deviations for both populations. For simplicity, one of the populations is centered at zero and the second is shifted by the mean difference, so we don't need to input multiple means.

``` r
sim_test_fun = function(n, mean_difference, sd1 = 1, sd2 = 1){
  x1 = rnorm(n, sd = sd1)
  x2 = rnorm(n, mean = mean_difference, sd = sd2)
  
  test1 = t.test(x1, x2, var.equal = (sd1 == sd2))
  test2 = wilcox.test(x1, x2)
  
  data.frame(
   wilcox = test2$p.value,
   ttest = test1$p.value
  )
}
```

This following code is a simulation using `tidyverse` syntax. I selected ranges of data that fit with some of my research focusing on differences of means on the log scale. Overall, this simulation can take a bit of time. Full disclosure: I used `plyr::ldply(1:10000, ...)` for the 10,000 simulations and utilized the parallelization option.

``` r
library(tidyverse)

test_data = expand.grid(
  simulations = 1:10000,
  sample_size = seq(4, 16, by = 2),
  log_difference = seq(0, 2.5, by = 0.5)
)

set.seed(100)
test_results = test_data %>%
  group_by(simulations, sample_size, log_difference) %>%
  do(sim_test_fun(n = .$sample_size, mean_difference = .$log_difference))
```

Now we compile the results assuming a typical significance level of 0.05. I also included an exact calculation of power for the t-test using the `pwr` library. For a mean difference of 0, we expect a rejection rate of about 0.05 (the false positive rate) which we see for the t-test but not quite the Wilcox. It is possible that 10,000 simulations is not enough.

``` r
library(pwr)

summary_results = test_results %>%
 group_by(sample_size, log_difference) %>%
 summarize(
   ttest_power = mean(ttest < 0.05),
   wilcox_power = mean(wilcox < 0.05),
   actual_ttest_pwr = pwr.t.test(n = unique(sample_size), d = unique(log_difference), 
                                 sig.level = 0.05, type = "two.sample")$power
 )



subset(summary_results, log_difference == 0) %>% knitr::kable()
```

|  sample\_size|  log\_difference|  ttest\_power|  wilcox\_power|  actual\_ttest\_pwr|
|-------------:|----------------:|-------------:|--------------:|-------------------:|
|             4|                0|        0.0480|         0.0269|                0.05|
|             6|                0|        0.0455|         0.0406|                0.05|
|             8|                0|        0.0506|         0.0499|                0.05|
|            10|                0|        0.0572|         0.0492|                0.05|
|            12|                0|        0.0490|         0.0448|                0.05|
|            14|                0|        0.0533|         0.0543|                0.05|
|            16|                0|        0.0502|         0.0476|                0.05|

Finally, a plot of the simulated power calculations relative to the calculated, exact values. The simulated t-test generally performs better than the Wilcox for these range of values. We see at the lowest sample size that there is a substantial loss of power for bigger differences and then a general, consistent reduction in power for smaller mean differences. The Wilcox results show a bit of variation and that means more simulations are probably required to get stable results, but the general trends seem stable.

``` r
summary_results_long = summary_results %>%
  subset(log_difference > 0) %>% #remove log-difference = 0 results
  gather(variable, value, ends_with("_power"))

ggplot(data = summary_results_long, aes(x = sample_size, y = value - actual_ttest_pwr,
                                        colour = variable, linetype = factor(log_difference),
                                        shape = factor(log_difference))) + 
  geom_hline(yintercept = 0, linetype = "dashed") +
  scale_y_continuous("Simulated power - calculated t-test power") +
  geom_point() +
  geom_line() +
  theme_bw()
```

![](plot-1.png)
