
<!-- README.md is generated from README.Rmd. Please edit that file -->

# analysistools

<!-- badges: start -->

[![check-standard](https://github.com/impact-initiatives/analysistools/actions/workflows/check-standard.yaml/badge.svg)](https://github.com/impact-initiatives/analysistools/actions/workflows/check-standard.yaml)
[![Codecov test
coverage](https://codecov.io/gh/impact-initiatives/analysistools/branch/main/graph/badge.svg)](https://app.codecov.io/gh/impact-initiatives/analysistools?branch=main)
<!-- badges: end -->

The goal of analysistools is to …

## Installation

You can install the development version of analysistools from
[GitHub](https://github.com/) with:

``` r
# install.packages("devtools")
devtools::install_github("impact-initiatives/analysistools")
```

## Example

This is a basic example which shows you how to calculate the mean:

``` r
library(analysistools)
somedata <- data.frame(aa = 1:10,
                      bb = rep(c("a","b"),5),
                      weights = rep(c(.5,1.5),5),
                      stratas = rep(c("strata_a", "strata_b"),5))
me_design <- srvyr::as_survey(somedata)
create_analysis_mean(me_design, analysis_var = "aa")
#> # A tibble: 1 × 13
#>   analysis_type analysis_var analysis_var_value group_var group_var_value  stat
#>   <chr>         <chr>        <chr>              <chr>     <chr>           <dbl>
#> 1 mean          aa           <NA>               <NA>      <NA>              5.5
#> # ℹ 7 more variables: stat_low <dbl>, stat_upp <dbl>, n <int>, n_total <int>,
#> #   n_w <dbl>, n_w_total <dbl>, analysis_key <chr>
create_analysis_mean(me_design, group_var = "bb", analysis_var = "aa")
#> # A tibble: 2 × 13
#>   analysis_type analysis_var analysis_var_value group_var group_var_value  stat
#>   <chr>         <chr>        <chr>              <chr>     <chr>           <dbl>
#> 1 mean          aa           <NA>               bb        a                   5
#> 2 mean          aa           <NA>               bb        b                   6
#> # ℹ 7 more variables: stat_low <dbl>, stat_upp <dbl>, n <int>, n_total <int>,
#> #   n_w <dbl>, n_w_total <dbl>, analysis_key <chr>
me_design_w <- srvyr::as_survey(somedata, weights = weights)
create_analysis_mean(me_design_w, analysis_var = "aa")
#> # A tibble: 1 × 13
#>   analysis_type analysis_var analysis_var_value group_var group_var_value  stat
#>   <chr>         <chr>        <chr>              <chr>     <chr>           <dbl>
#> 1 mean          aa           <NA>               <NA>      <NA>             5.75
#> # ℹ 7 more variables: stat_low <dbl>, stat_upp <dbl>, n <int>, n_total <int>,
#> #   n_w <dbl>, n_w_total <dbl>, analysis_key <chr>
create_analysis_mean(me_design_w, group_var = "bb", analysis_var = "aa")
#> # A tibble: 2 × 13
#>   analysis_type analysis_var analysis_var_value group_var group_var_value  stat
#>   <chr>         <chr>        <chr>              <chr>     <chr>           <dbl>
#> 1 mean          aa           <NA>               bb        a                   5
#> 2 mean          aa           <NA>               bb        b                   6
#> # ℹ 7 more variables: stat_low <dbl>, stat_upp <dbl>, n <int>, n_total <int>,
#> #   n_w <dbl>, n_w_total <dbl>, analysis_key <chr>
```

This is a basic example which shows you how to calculate the proportion
for select one:

``` r
somedata <- data.frame(groups = sample(c("group_a", "group_b"), size = 100,
                       replace = TRUE),
                       value = sample(c("a", "b", "c"), size = 100, replace = TRUE,
                       prob = c(.6,.4,.1)))

create_analysis_prop_select_one(srvyr::as_survey(somedata, strata = groups),
                                group_var = NA,
                                analysis_var = "value",
                                level = .95)
#> # A tibble: 3 × 13
#>   analysis_type  analysis_var analysis_var_value group_var group_var_value  stat
#>   <chr>          <chr>        <chr>              <chr>     <chr>           <dbl>
#> 1 prop_select_o… value        a                  <NA>      <NA>             0.53
#> 2 prop_select_o… value        b                  <NA>      <NA>             0.41
#> 3 prop_select_o… value        c                  <NA>      <NA>             0.06
#> # ℹ 7 more variables: stat_low <dbl>, stat_upp <dbl>, n <int>, n_total <int>,
#> #   n_w <dbl>, n_w_total <dbl>, analysis_key <chr>
create_analysis_prop_select_one(srvyr::as_survey(somedata, strata = groups),
                                group_var = "groups",
                                analysis_var = "value",
                                level = .95)
#> # A tibble: 6 × 13
#>   analysis_type analysis_var analysis_var_value group_var group_var_value   stat
#>   <chr>         <chr>        <chr>              <chr>     <chr>            <dbl>
#> 1 prop_select_… value        a                  groups    group_a         0.547 
#> 2 prop_select_… value        b                  groups    group_a         0.415 
#> 3 prop_select_… value        c                  groups    group_a         0.0377
#> 4 prop_select_… value        a                  groups    group_b         0.511 
#> 5 prop_select_… value        b                  groups    group_b         0.404 
#> 6 prop_select_… value        c                  groups    group_b         0.0851
#> # ℹ 7 more variables: stat_low <dbl>, stat_upp <dbl>, n <int>, n_total <int>,
#> #   n_w <dbl>, n_w_total <dbl>, analysis_key <chr>
```

This is a basic example which shows you how to calculate the ratio
between 2 numeric variables:

``` r
school_ex <- data.frame(
  hh = c("hh1", "hh2", "hh3", "hh4"),
  num_children = c(3, 0, 2, NA),
  num_enrolled = c(3, NA, 0, NA),
  num_attending = c(1, NA, NA, NA),
  group = c("a", "a", "b", "b")
)
me_design <- srvyr::as_survey(school_ex)
```

Default value will give a ratio of 0.2 as there are 1 child out of 5
attending school. In the hh3, the NA is present because there is a skip
logic, there cannot be a child attending as none are enrolled. The
number of household counted, n, is equal to 2, as there are 2 households
only having child.

``` r
create_analysis_ratio(me_design,
                     analysis_var_numerator = "num_attending",
                     analysis_var_denominator ="num_children")
#> # A tibble: 1 × 13
#>   analysis_type analysis_var  analysis_var_value group_var group_var_value  stat
#>   <chr>         <chr>         <chr>              <chr>     <chr>           <dbl>
#> 1 ratio         num_attendin… NA ~/~ NA          <NA>      <NA>              0.2
#> # ℹ 7 more variables: stat_low <dbl>, stat_upp <dbl>, n <int>, n_total <int>,
#> #   n_w <dbl>, n_w_total <dbl>, analysis_key <chr>
```

If numerator_NA_to_0 is set to FALSE, ratio will be 1/3, as hh3 with 2
children and NA for attending will be removed with the na.rm = T inside
the survey_ratio calculation. The number of household used in the
calculation is 1.

``` r
create_analysis_ratio(me_design,
                     analysis_var_numerator = "num_attending",
                     analysis_var_denominator ="num_children",
                     numerator_NA_to_0 = FALSE)
#> Warning: There were 2 warnings in `dplyr::summarise()`.
#> The first warning was:
#> ℹ In argument: `srvyr::survey_ratio(...)`.
#> Caused by warning in `qt()`:
#> ! NaNs produced
#> ℹ Run `dplyr::last_dplyr_warnings()` to see the 1 remaining warning.
#> # A tibble: 1 × 13
#>   analysis_type analysis_var  analysis_var_value group_var group_var_value  stat
#>   <chr>         <chr>         <chr>              <chr>     <chr>           <dbl>
#> 1 ratio         num_attendin… NA ~/~ NA          <NA>      <NA>            0.333
#> # ℹ 7 more variables: stat_low <dbl>, stat_upp <dbl>, n <int>, n_total <int>,
#> #   n_w <dbl>, n_w_total <dbl>, analysis_key <chr>
```

If filter_denominator_0 is set to FALSE, ratio will be 0.2 as there are
1 child out of 5 attending school. In the hh3, the NA is present because
there is a skip logic, there cannot be a child attending as none are
enrolled. The number of household counted, n, is equal to 3 instead 2.
The household with 0 child is counted in the n.

``` r
create_analysis_ratio(me_design,
                     analysis_var_numerator = "num_attending",
                     analysis_var_denominator ="num_children",
                     numerator_NA_to_0 = FALSE)
#> Warning: There were 2 warnings in `dplyr::summarise()`.
#> The first warning was:
#> ℹ In argument: `srvyr::survey_ratio(...)`.
#> Caused by warning in `qt()`:
#> ! NaNs produced
#> ℹ Run `dplyr::last_dplyr_warnings()` to see the 1 remaining warning.
#> # A tibble: 1 × 13
#>   analysis_type analysis_var  analysis_var_value group_var group_var_value  stat
#>   <chr>         <chr>         <chr>              <chr>     <chr>           <dbl>
#> 1 ratio         num_attendin… NA ~/~ NA          <NA>      <NA>            0.333
#> # ℹ 7 more variables: stat_low <dbl>, stat_upp <dbl>, n <int>, n_total <int>,
#> #   n_w <dbl>, n_w_total <dbl>, analysis_key <chr>
```

For weigths and group:

``` r
set.seed(8988)
somedata <- data.frame(
 groups = rep(c("a", "b"), 50),
 children_518 = sample(0:5, 100, replace = TRUE),
 children_enrolled = sample(0:5, 100, replace = TRUE)
) %>%
 dplyr::mutate(children_enrolled = ifelse(children_enrolled > children_518,
                                          children_518,
                                          children_enrolled
 ))
somedata[["weights"]] <- ifelse(somedata$groups == "a", 1.33, .67)
create_analysis_ratio(srvyr::as_survey(somedata, weights = weights, strata = groups),
                     group_var = NA,
                     analysis_var_numerator = "children_enrolled",
                     analysis_var_denominator = "children_518",
                     level = 0.95)
#> # A tibble: 1 × 13
#>   analysis_type analysis_var  analysis_var_value group_var group_var_value  stat
#>   <chr>         <chr>         <chr>              <chr>     <chr>           <dbl>
#> 1 ratio         children_enr… NA ~/~ NA          <NA>      <NA>            0.639
#> # ℹ 7 more variables: stat_low <dbl>, stat_upp <dbl>, n <int>, n_total <int>,
#> #   n_w <dbl>, n_w_total <dbl>, analysis_key <chr>
create_analysis_ratio(srvyr::as_survey(somedata, weights = weights, strata = groups),
                     group_var = "groups",
                     analysis_var_numerator = "children_enrolled",
                     analysis_var_denominator = "children_518",
                     level = 0.95)
#> # A tibble: 2 × 13
#>   analysis_type analysis_var  analysis_var_value group_var group_var_value  stat
#>   <chr>         <chr>         <chr>              <chr>     <chr>           <dbl>
#> 1 ratio         children_enr… NA ~/~ NA          groups    a               0.670
#> 2 ratio         children_enr… NA ~/~ NA          groups    b               0.578
#> # ℹ 7 more variables: stat_low <dbl>, stat_upp <dbl>, n <int>, n_total <int>,
#> #   n_w <dbl>, n_w_total <dbl>, analysis_key <chr>
```
