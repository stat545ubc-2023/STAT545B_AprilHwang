Assignment B-1: Making a function
================
April Hwang

# Setup

``` r
#hide package start up message
suppressPackageStartupMessages(library(tidyverse)) 
suppressPackageStartupMessages(library(testthat)) 

#load libraries
library(tidyverse)
library(datateachr) 
library(testthat)
```

<br>

# Make a Function

As per the requirements for **exercise 2**, this section has been
documented using roxygen2 tags. It contains a section title and a
function description. The argument has been documented with `@param` and
`@return` tags. `@param` has been named as below as to provide
information regarding the allowed inputs (including the format necessary
with \$ sign) as well as a concise description of what the parameter
does.

``` r
#' @title max_minus_min
#' 
#' @description
#' Subtract min value from max value. Remove NA.
#' 
#' @param x Input dataset$column name. Used to take num values from column
#' @return A number
#' 
max_minus_min <- function(x, na.rm = TRUE) {
  max(x, na.rm = na.rm) - min(x, na.rm = na.rm)
}
```

<br>

# Examples

**Example 1**: For this example, I will calculate the difference between
max and min for `original_price`.

I will familiarize myself with the dataset by glancing at
`original_price`. I will start by sorting the column from highest to
lowest. From this, I can see that the two highest games cost \$730,640
and \$650,560. They are likely outliers and will be removed.

``` r
#original_price highest to lowest
steam_games %>%
  select(name, original_price) %>%
  arrange(desc(original_price))
```

    ## # A tibble: 40,833 × 2
    ##    name                                            original_price
    ##    <chr>                                                    <dbl>
    ##  1 Railgunners                                            730640 
    ##  2 Slime-san: Superslime Edition                          650560 
    ##  3 Valve Index VR Kit                                        999 
    ##  4 Strata Spaces VR – Professional Edition Upgrade           995 
    ##  5 Valve Index Headset + Controllers                         749 
    ##  6 Crankies Workshop: Bozzbot Assembly                       625.
    ##  7 Crankies Workshop: Grizzbot Assembly                      625.
    ##  8 Fitzzle Wise Owls                                         625.
    ##  9 Digit Daze                                                625.
    ## 10 Fitzzle Majestic Eagles                                   625.
    ## # ℹ 40,823 more rows

<br>

I will now sort `original_price` from lowest to highest. From this, I
can see that many games are actually free. For the purpose of
demonstrating the `max_minus_min()` function, I will remove the free
games.

``` r
#original_price lowest to highest
steam_games %>%
  select(name, original_price) %>%
  arrange(original_price)
```

    ## # A tibble: 40,833 × 2
    ##    name                          original_price
    ##    <chr>                                  <dbl>
    ##  1 EVE Online                                 0
    ##  2 TERA                                       0
    ##  3 War Robots                                 0
    ##  4 Tree of Savior (English Ver.)              0
    ##  5 Wizard101                                  0
    ##  6 Middle-earth™: Shadow of War™              0
    ##  7 The Lord of the Rings Online™              0
    ##  8 The Elder Scrolls®: Legends™               0
    ##  9 Realm Royale                               0
    ## 10 Dungeons & Dragons Online®                 0
    ## # ℹ 40,823 more rows

<br>

The `max_minus_min()` function shows that the difference between the
maximum original price (999.00) and minimum original price (0.5) is
998.5.

``` r
#eliminate two highest priced games and the free games
updated_sg <- steam_games %>%
  select(name, original_price) %>%
  arrange(desc(original_price)) %>%
  filter(!row_number() %in% c(1, 2)) %>%
  filter(original_price != 0)

#calculate max minus min for original price
max_minus_min(updated_sg$original_price)
```

    ## [1] 998.5

<br>

**Example 2**: For this example, I will group by `type` (app, bundle and
sub; remove NA) and calculate the difference between max and min of
`original_price` of each group.

``` r
#group by types and calculate max minus min for each group
grouped_sg <- steam_games %>%
  arrange(desc(original_price)) %>%
  filter(!row_number() %in% c(1, 2)) %>%
  filter(original_price != 0) %>%
  filter(!is.na(types)) %>%
  group_by(types) %>%
  summarise(price_diff = max_minus_min(original_price))

print(grouped_sg)
```

    ## # A tibble: 3 × 2
    ##   types  price_diff
    ##   <chr>       <dbl>
    ## 1 app          994.
    ## 2 bundle       527.
    ## 3 sub          996.

<br>

**Example 3**: I will calculate the max minus min difference in the
achievement number. I will familiarize myself with the dataset by
examining the column, `achievements`. From this, I can see that the
highest achievement number is 9821.

``` r
#achievements highest to lowest
steam_games %>%
  select(name, achievements) %>%
  arrange(desc(achievements))
```

    ## # A tibble: 40,833 × 2
    ##    name                              achievements
    ##    <chr>                                    <dbl>
    ##  1 LOGistICAL                                9821
    ##  2 Achievement Machine                       5000
    ##  3 USA 2020                                  5000
    ##  4 Math Problem Challenge                    5000
    ##  5 Trash Story                               5000
    ##  6 Trivia Vault Olympics Trivia              5000
    ##  7 Math Speed Challenge                      5000
    ##  8 18+                                       5000
    ##  9 Trivia Vault: Classic Rock Trivia         5000
    ## 10 Solar Battle Glargaz                      5000
    ## # ℹ 40,823 more rows

<br>

I will now sort `achievements` from lowest to highest. From this, I can
see that the lowest achievement number is 1.

``` r
#achievements lowest to highest
steam_games %>%
  select(name, achievements) %>%
  arrange(achievements)
```

    ## # A tibble: 40,833 × 2
    ##    name                                 achievements
    ##    <chr>                                       <dbl>
    ##  1 Hot Dogs, Horseshoes & Hand Grenades            1
    ##  2 To the Moon                                     1
    ##  3 Devil Daggers                                   1
    ##  4 SUPERHOT: MIND CONTROL DELETE                   1
    ##  5 Ymir                                            1
    ##  6 Finding Paradise                                1
    ##  7 Borderless Gaming                               1
    ##  8 Get To The Orange Door                          1
    ##  9 Feather                                         1
    ## 10 A Bird Story                                    1
    ## # ℹ 40,823 more rows

<br>

Based on the above information, I will not remove any outliers and run
the function with the dataset as is. The `max_minus_min()` function
shows that the difference between the maximum achievement number (9821)
and minimum achievement number (1) is 9820.

``` r
#calculate max minus min for achievements
max_minus_min(steam_games$achievements)
```

    ## [1] 9820

<br>

# Test the Function

I will test that the `max_minus_min()` function on `original_price` runs
as expected using the `testthat` package.

``` r
#expect_equal
test_that('Expected value is correct', {
  expect_equal(max_minus_min(updated_sg$original_price), 998.5)
})
```

    ## Test passed 🌈

``` r
#expect_gt and expect_lt
test_that('Output is greater than 0 and less than 1000', {
  expect_gt(max_minus_min(updated_sg$original_price), 0)
  expect_lt(max_minus_min(updated_sg$original_price), 1000)
})
```

    ## Test passed 😸

``` r
#expect_true and expect_false
test_that('Expected value results in TRUE', {
  expect_true(max_minus_min(updated_sg$original_price) == 998.5)
  expect_false(max_minus_min(updated_sg$original_price) != 998.5)
})
```

    ## Test passed 😸
