The Simpson’s Paradox
================
Miguel Omar
2023-08-06

## Introduction

The Simpson’s Paradox is a fascinating statistical phenomenon that
challenges our intuition and highlights the importance of careful data
analysis. This paradox underscores the critical role of proper
statistical analysis and the potential risks of drawing conclusions
without considering all relevant factors.

In this R based markdown, we’ll explore this phenomenon through the
Palmer Penguins dataset. By the end of this exploration, you’ll have
gained a deeper understanding of Simpson’s Paradox and its implications
for data interpretation.

Are you ready for this journey?

## Hands on

### Importing libraries and the dataset

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.2     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.2     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.1     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(ggthemes) # Optional
library(ggpubr)
library(palmerpenguins) # Package needed to load the penguins dataset

data <- penguins
```

Let’s have a look to our data:

``` r
glimpse(data)
```

    ## Rows: 344
    ## Columns: 8
    ## $ species           <fct> Adelie, Adelie, Adelie, Adelie, Adelie, Adelie, Adel…
    ## $ island            <fct> Torgersen, Torgersen, Torgersen, Torgersen, Torgerse…
    ## $ bill_length_mm    <dbl> 39.1, 39.5, 40.3, NA, 36.7, 39.3, 38.9, 39.2, 34.1, …
    ## $ bill_depth_mm     <dbl> 18.7, 17.4, 18.0, NA, 19.3, 20.6, 17.8, 19.6, 18.1, …
    ## $ flipper_length_mm <int> 181, 186, 195, NA, 193, 190, 181, 195, 193, 190, 186…
    ## $ body_mass_g       <int> 3750, 3800, 3250, NA, 3450, 3650, 3625, 4675, 3475, …
    ## $ sex               <fct> male, female, female, NA, female, male, female, male…
    ## $ year              <int> 2007, 2007, 2007, 2007, 2007, 2007, 2007, 2007, 2007…

In a quick glance we can notice that we have morphological data from 344
penguins. Data regarding bill and flipper dimensions, body mass are
collected together with the sex, species or the year the measurement was
conducted.

### Looking for relationships (the Simpson’s Paradox)

Now that we know a bit more about our data, we can start looking for
relationships between morphological variables. A very common analysis
with this dataset is how the bill length and depth are related. A
scatterplot can help us to figure it out:

``` r
p1 <- ggplot(data = data, aes(x = bill_depth_mm, y = bill_length_mm)) +
  geom_point() +
  geom_smooth(method = "lm") +
  stat_cor() +
  labs(title = "Bill Depth vs. Bill Length",
       x = "Bill Depth (mm)",
       y = "Bill Length (mm)")
p1
```

    ## `geom_smooth()` using formula = 'y ~ x'

    ## Warning: Removed 2 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 2 rows containing non-finite values (`stat_cor()`).

    ## Warning: Removed 2 rows containing missing values (`geom_point()`).

![](simpson_paradox_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

The scatterplot shows us a significant and negative relationship between
the bill depth and the bill length of the penguins. That is, the wider
the less long the bills will be. If you don’t pay enough attention you
can get confused for different reasons:

- The statistical significance of the relationship
- You could assume that, to maintain a given bill mass balance, it makes
  sense that the wider the bill is the less long it will be

At the end of the analysis you can conclude that the bill length and
depth are negatively correlated. But, what if we’re missing relevant
information?

As we noticed at the beginning, one the variables collected in the
dataset are the species each penguin belongs to. Three different species
are available (Adelie, Chinstrap and Gentoo).

Let’s try to repeat our scatterplot, but this time grouping by species:

``` r
p2 <- ggplot(data = data, aes(x = bill_depth_mm, y = bill_length_mm, color = species)) +
  geom_point() +
  geom_smooth(method = "lm") +
  scale_color_colorblind() +
  stat_cor() +
  labs(title = "Bill Depth vs. Bill Length by species",
       x = "Bill Depth (mm)",
       y = "Bill Length (mm)")
p2
```

    ## `geom_smooth()` using formula = 'y ~ x'

    ## Warning: Removed 2 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 2 rows containing non-finite values (`stat_cor()`).

    ## Warning: Removed 2 rows containing missing values (`geom_point()`).

![](simpson_paradox_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

Wow! Using the same data, now we found that there’s a significant,
moderate to high positive relationship between the bill depth and
length. That is, the wider the longer the bill will be. Just the
opposite than before! How can this be possible? We can hypothesize the
following:

- We have different penguin species
- As different species, they may have morphological differences.
  Different size, body mass, and of course different bill depths and
  lengths
- Those bill differences are INDEPENDENT from the relationship between
  their depth and length

And here is where the Simpson’s Paradox appears: analyzing the data as a
whole, we find a relationship which is completely reversed when we
observe the same data grouped by a specific variable. Omitting a
meaningful variable can make our conclusions partially or completely
wrong. Something that could have important consequences in different
fields such as business decision-making, health policies…

Now, let’s plot both scatterplots together to clearly understand this
paradox at a glance:

``` r
ggarrange(p1, p2, legend = "none") %>% annotate_figure(top = text_grob("The Simpson's Paradox", size = 15))
```

    ## `geom_smooth()` using formula = 'y ~ x'

    ## Warning: Removed 2 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 2 rows containing non-finite values (`stat_cor()`).

    ## Warning: Removed 2 rows containing missing values (`geom_point()`).

    ## `geom_smooth()` using formula = 'y ~ x'

    ## Warning: Removed 2 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 2 rows containing non-finite values (`stat_cor()`).

    ## Warning: Removed 2 rows containing missing values (`geom_point()`).

![](simpson_paradox_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

## Conclusions

The Simpson’s Paradox serves as a powerful reminder that a comprehensive
understanding of data requires more than just a surface-level
examination. Here we used a simple dataset which could be easy to catch
to an experienced eye, but in our daily work we can face situations in
which we can fall into the silent deceptions of statistics.

Hope this markdown helped you to understand this phenomenon and its
implications. And also that it helped you to strength your analytical
mindset. Maybe in the future you’ll remember the day you learn about it!

Thank you for reading and joining me.
