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
  ) %>%
  knitr::kable()
```

| group     |   a |   b |
|:----------|----:|----:|
| treatment |   4 |   8 |
| control   |   3 |   6 |

## bind\_rows

import LotR movie words data

``` r
fellowship_df = 
  read_excel("data/LotR_Words.xlsx", range = "B3:D6") %>%
  mutate(movie = "fellowship_rings")

two_towers_df = 
  read_excel("data/LotR_Words.xlsx", range = "F3:H6") %>%
  mutate(movie = "two_towers")

return_df = 
  read_excel("data/LotR_Words.xlsx", range = "J3:L6") %>%
  mutate(movie = "return_king")

lotr_df =
  bind_rows(fellowship_df, two_towers_df, return_df) %>%
  janitor::clean_names() %>%
  pivot_longer(
    female:male,
    names_to = "sex",
    values_to = "words"
  ) %>%
  relocate(movie)
```

## joins

Look at FAS data

``` r
litters_df =
  read_csv("data/FAS_litters.csv") %>%
  janitor::clean_names() %>%
  separate(group, into = c("dose", "day_of_tx"), 3) %>%
  relocate(litter_number)
```

    ## Rows: 49 Columns: 8

    ## -- Column specification --------------------------------------------------------
    ## Delimiter: ","
    ## chr (2): Group, Litter Number
    ## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...

    ## 
    ## i Use `spec()` to retrieve the full column specification for this data.
    ## i Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
pups_df =
  read_csv("data/FAS_pups.csv") %>%
  janitor::clean_names() %>%
  mutate(sex = recode(sex, '1' = "male", '2' = "female"))
```

    ## Rows: 313 Columns: 6

    ## -- Column specification --------------------------------------------------------
    ## Delimiter: ","
    ## chr (1): Litter Number
    ## dbl (5): Sex, PD ears, PD eyes, PD pivot, PD walk

    ## 
    ## i Use `spec()` to retrieve the full column specification for this data.
    ## i Specify the column types or set `show_col_types = FALSE` to quiet this message.

Join!

``` r
fas_df = 
  left_join(pups_df, litters_df, by = "litter_number") %>%
  relocate(litter_number, dose, day_of_tx)
```

bind\_rows() instead of rbind(), pivot\_wider() instead of spread(), and
pivot\_longer() instead of gather()
