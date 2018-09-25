---
layout: post
title: R package misc&#58; For everyday analyses!
---

## Summary
I wrote my first R package that contains some useful functions I use on a daily basis. I got tired of loading them in my working environment every time so I decided to make an R package. Here is a summary of the functions version 0.1.0 contains:

* [`write_excel`](#write_excel) - copies data frame to clipboard which makes it easy to copy data, e.g. to Excel
* `parametrized_query` - parametrize SQL query.
* `analyse_var` - analyse variable depending on AB test group
* `np_mean_test` - non-parametric bootstrap test of mean
* [`np_stat_test`](#np_stat_test)- non-parametric bootstrap test of any statistics
* `split_per_group`] - splits variable from one column into list of vectors depending on any other variable e.g. AB test group
* `get_value` - for a function defined by points return value for an x

Hope you find them useful!

## Installation
This package is avalilabe through GitHub. You will need package *devtools* to install it.

```R
#If you don't have devtools package
install.packages("devtools")
library(devtools)

# If you have devtools run from here
install_github("eliobartos/misc")
library(misc)
```

To read my beautiful documentation run 
```R
help(package = misc)
```
Next I will show you how to use some functions from *misc* package and how they came to life.

<a name="write_excel"></a>
### write_excel

How many times did you need to copy some data from R into Excel? Did you copy it from the console, or did you use `View()` and then copy it? Well I did it like that at the beginning and it was awful. Then with some Google search found this to be really good way to copy data to Excel or anywhere you want. Just add this function at the end of your pipeline and it is like you pressed CTRL+C, go to Excel and press CTRL+V.

```R
data(mtcars)

mtcars %>% 
  group_by(cyl) %>% 
  summarise(hp = mean(hp)) %>% 
  write_excel()
```

More logical name for this function would be `copy_to_clipbord` or `ctc` for short, but I like `write_excel` because I'm used to it now.

<a name="np_stat_test"></a>
### np_stat_test

Let say that you have two vectors that have data about how much money people spend in two different towns on food. Money spent is usually very skewed distribution and hard to analyse. This function enables you to test difference in mean, medina, other quantiles or any other statistics that you find interesting.

Idea is simple, if there is no difference, then both groups came from the same distribution. This is our null hypothesis. We gather all observations together and than sample two groups of sizes that input vectors were. We calculate statistics of interest and difference in that statistics. We do that many many times. Then we compare our observed difference with distribution od differences that we got. If our observed difference is very unlikely, then our null hypothesis is not valid.

Let's generate some sample data:
```R
x = rnorm(300, mean = 5, sd = 1)
y = rnorm(200, mean = 4, sd = 1.5)
y2 = c(y, 110)
```

We added one extreme observation to y2, so that te mean is not so different when comparing x and y2.

