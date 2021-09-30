Tidy Data
================

## pivot longer

``` r
pulse_df =
  read_sas("./data/public_pulse_data.sas7bdat") %>%
  janitor::clean_names()
```

Pivot

``` r
pulse_tidy = 
  pulse_df %>%
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    names_prefix = "bdi_score_",
    values_to = "bdi"
  ) %>%
  mutate(
    visit = replace(visit, visit == "bl","00m"),
    visit = factor(visit)
  )
```

## pivot\_wider

make up results data table

``` r
analysis_df =
  tibble(
    group = c("treatment", "treatment", "control", "control"),
    time = c("a", "b", "a", "b"),
    group_mean = c(4, 8, 3, 6)
  )

analysis_df %>%
  pivot_wider(
    names_from = "time",
    values_from = "group_mean"
  )
```

    ## # A tibble: 2 x 3
    ##   group         a     b
    ##   <chr>     <dbl> <dbl>
    ## 1 treatment     4     8
    ## 2 control       3     6
