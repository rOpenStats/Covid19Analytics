
<!-- README.md is generated from README.Rmd. Please edit that file -->

# COVID19analytics

<!-- . -->

This package curate (downloads, clean, consolidate, smooth) [data from
Johns Hokpins](https://github.com/CSSEGISandData/COVID-19/) for
analysing international outbreak of COVID-19.

It includes several visualizations of the COVID-19 international
outbreak.

Yanchang Zhao, COVID-19 Data Analysis with Tidyverse and Ggplot2 -
China. RDataMining.com, 2020.

URL:
<http://www.rdatamining.com/docs/Coronavirus-data-analysis-china.pdf>.

- COVID19DataProcessor generates curated series
- [visualizations](https://www.r-bloggers.com/coronavirus-data-analysis-with-r-tidyverse-and-ggplot2/)
  by [Yanchang Zhao](https://www.r-bloggers.com/author/yanchang-zhao/)
  are included in ReportGenerator R6 object
- More visualizations included int ReportGeneratorEnhanced R6 object
- Visualizations ReportGeneratorDataComparison compares all countries
  counting epidemy day 0 when confirmed cases \> n (i.e. n = 100).

# Consideration

Data is still noisy because there are missing data from some regions in
some days. We are working on in it.

# Package

<!-- badges: start -->

| Release                                                                                                              | Usage                                                                                                    | Development                                                                                                                                                                                            |
|:---------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|                                                                                                                      | [![minimal R version](https://img.shields.io/badge/R%3E%3D-3.4.0-blue.svg)](https://cran.r-project.org/) | [![Travis](https://travis-ci.org/rOpenStats/COVID19analytics.svg?branch=master)](https://travis-ci.org/rOpenStats/COVID19analytics)                                                                    |
| [![CRAN](http://www.r-pkg.org/badges/version/COVID19analytics)](https://cran.r-project.org/package=COVID19analytics) |                                                                                                          | [![codecov](https://codecov.io/gh/rOpenStats/COVID19analytics/branch/master/graph/badge.svg)](https://codecov.io/gh/rOpenStats/COVID19analytics)                                                       |
|                                                                                                                      |                                                                                                          | [![Project Status: Active – The project has reached a stable, usable state and is being actively developed.](https://www.repostatus.org/badges/latest/active.svg)](https://www.repostatus.org/#active) |

<!-- badges: end -->

# How to get started (Development version)

Install the R package using the following commands on the R console:

``` r
# install.packages("devtools")
devtools::install_github("rOpenStats/COVID19analytics", build_opts = NULL)
```

# How to use it

``` r
library(COVID19analytics) 
#> Warning: replacing previous import 'ggplot2::Layout' by 'lgr::Layout' when
#> loading 'COVID19analytics'
#> Warning: replacing previous import 'readr::col_factor' by 'scales::col_factor'
#> when loading 'COVID19analytics'
#> Warning: replacing previous import 'magrittr::not' by 'testthat::not' when
#> loading 'COVID19analytics'
#> Warning: replacing previous import 'dplyr::matches' by 'testthat::matches' when
#> loading 'COVID19analytics'
#> Warning: replacing previous import 'readr::edition_get' by
#> 'testthat::edition_get' when loading 'COVID19analytics'
#> Warning: replacing previous import 'magrittr::equals' by 'testthat::equals' when
#> loading 'COVID19analytics'
#> Warning: replacing previous import 'magrittr::is_less_than' by
#> 'testthat::is_less_than' when loading 'COVID19analytics'
#> Warning: replacing previous import 'readr::local_edition' by
#> 'testthat::local_edition' when loading 'COVID19analytics'
#> Warning: replacing previous import 'testthat::matches' by 'tidyr::matches' when
#> loading 'COVID19analytics'
#> Warning: replacing previous import 'magrittr::extract' by 'tidyr::extract' when
#> loading 'COVID19analytics'
library(dplyr) 
#> 
#> Attaching package: 'dplyr'
#> The following objects are masked from 'package:stats':
#> 
#>     filter, lag
#> The following objects are masked from 'package:base':
#> 
#>     intersect, setdiff, setequal, union
```

``` r
data.processor <- COVID19DataProcessor$new(provider = "JohnsHopkingsUniversity", missing.values = "imputation")

#dummy <- data.processor$preprocess() is setupData + transform is the preprocess made by data provider
dummy <- data.processor$setupData()
#> INFO  [07:51:31.629]  {stage: `processor-setup`}
#> INFO  [07:51:31.865] Checking required downloaded  {downloaded.max.date: `2023-01-09`, daily.update.time: `21:00:00`, current.datetime: `2023-01-11 07:51:31`, download.flag: `TRUE`}
#> INFO  [07:51:34.888] Checking required downloaded  {downloaded.max.date: `2023-01-09`, daily.update.time: `21:00:00`, current.datetime: `2023-01-11 07:51:34`, download.flag: `TRUE`}
#> INFO  [07:51:36.562] Checking required downloaded  {downloaded.max.date: `2023-01-09`, daily.update.time: `21:00:00`, current.datetime: `2023-01-11 07:51:36`, download.flag: `TRUE`}
#> INFO  [07:51:38.103]  {stage: `data loaded`}
#> INFO  [07:51:38.105]  {stage: `data-setup`}
dummy <- data.processor$transform()
#> INFO  [07:51:38.108] Executing transform
#> INFO  [07:51:38.109] Executing consolidate
#> INFO  [07:52:13.160]  {stage: `consolidated`}
#> INFO  [07:52:13.161] Executing standarize
#> INFO  [07:52:17.230] gathering DataModel
#> INFO  [07:52:17.231]  {stage: `datamodel-setup`}
# Curate is the process made by missing values method
dummy <- data.processor$curate()
#> INFO  [07:52:17.241]  {stage: `loading-aggregated-data-model`}
#> Warning in countrycode_convert(sourcevar = sourcevar, origin = origin, destination = dest, : Some values were not matched unambiguously: Antarctica
#> Warning in countrycode_convert(sourcevar = sourcevar, origin = origin, destination = dest, : Some values were not matched unambiguously: Micronesia
#> Warning in countrycode_convert(sourcevar = sourcevar, origin = origin, destination = dest, : Some values were not matched unambiguously: MS Zaandam
#> Warning in countrycode_convert(sourcevar = sourcevar, origin = origin, destination = dest, : Some values were not matched unambiguously: Summer Olympics 2020
#> Warning in countrycode_convert(sourcevar = sourcevar, origin = origin, destination = dest, : Some values were not matched unambiguously: Winter Olympics 2022
#> INFO  [07:52:21.891]  {stage: `calculating-rates`}
#> INFO  [07:52:22.236]  {stage: `making-data-comparison`}
#> INFO  [07:52:38.136]  {stage: `applying-missing-values-method`}
#> INFO  [07:52:38.138]  {stage: `Starting first imputation`}
#> INFO  [07:52:38.192]  {stage: `calculating-rates`}
#> INFO  [07:52:38.567]  {stage: `making-data-comparison-2`}
#> INFO  [07:52:54.635]  {stage: `calculating-top-countries`}
#> INFO  [07:52:54.672]  {stage: `curated`}

current.date <- max(data.processor$getData()$date)

rg <- ReportGeneratorEnhanced$new(data.processor)
rc <- ReportGeneratorDataComparison$new(data.processor = data.processor)


top.countries <- data.processor$top.countries
international.countries <- unique(c(data.processor$top.countries,
                                    "China", "Japan", "Singapore", "Korea, South"))
africa.countries <- sort(data.processor$countries$getCountries(division = "continent", name = "Africa"))
```

``` r
# Top 10 daily cases confirmed increment
(data.processor$getData() %>%
  filter(date == current.date) %>%
  select(country, date, rate.inc.daily, confirmed.inc, confirmed, deaths, deaths.inc) %>%
  arrange(desc(confirmed.inc)) %>%
  filter(confirmed >=10))[1:10,]
#> # A tibble: 10 × 7
#> # Groups:   country [10]
#>    country      date       rate.inc.daily confirmed.inc confirmed deaths death…¹
#>    <chr>        <date>              <dbl>         <int>     <int>  <int>   <int>
#>  1 Japan        2023-01-10         0.0026         78982  30670001 6.04e4     253
#>  2 Brazil       2023-01-10         0.0021         75218  36552432 6.95e5     206
#>  3 US           2023-01-10         0.0006         59695 101345042 1.10e6     864
#>  4 Korea, South 2023-01-10         0.0018         54343  29654090 3.27e4      76
#>  5 Thailand     2023-01-10         0.0062         29164   4752782 3.47e4    1018
#>  6 Taiwan*      2023-01-10         0.0028         25049   9097554 1.56e4      26
#>  7 Germany      2023-01-10         0.0006         22119  37562191 1.63e5     269
#>  8 France       2023-01-10         0.0003         11773  39625664 1.64e5     115
#>  9 China        2023-01-10         0.002           9379   4804906 1.78e4      75
#> 10 Austria      2023-01-10         0.0005          3019   5731160 2.15e4      27
#> # … with abbreviated variable name ¹​deaths.inc
```

``` r
# Top 10 daily deaths increment
(data.processor$getData() %>%
  filter(date == current.date) %>%
  select(country, date, rate.inc.daily, confirmed.inc, confirmed, deaths, deaths.inc) %>%
  arrange(desc(deaths.inc)))[1:10,]
#> # A tibble: 10 × 7
#> # Groups:   country [10]
#>    country      date       rate.inc.daily confirmed.inc confirmed deaths death…¹
#>    <chr>        <date>              <dbl>         <int>     <int>  <int>   <int>
#>  1 Thailand     2023-01-10         0.0062         29164   4752782 3.47e4    1018
#>  2 US           2023-01-10         0.0006         59695 101345042 1.10e6     864
#>  3 Germany      2023-01-10         0.0006         22119  37562191 1.63e5     269
#>  4 Japan        2023-01-10         0.0026         78982  30670001 6.04e4     253
#>  5 Brazil       2023-01-10         0.0021         75218  36552432 6.95e5     206
#>  6 France       2023-01-10         0.0003         11773  39625664 1.64e5     115
#>  7 Korea, South 2023-01-10         0.0018         54343  29654090 3.27e4      76
#>  8 China        2023-01-10         0.002           9379   4804906 1.78e4      75
#>  9 Russia       2023-01-10         0.0001          3010  21524480 3.86e5      46
#> 10 Australia    2023-01-10         0.0001          1150  11211305 1.74e4      31
#> # … with abbreviated variable name ¹​deaths.inc
```

``` r
rg$ggplotTopCountriesStackedBarDailyInc(included.countries = africa.countries,
                                                  countries.text = "Africa")
#> Warning: Removed 318 rows containing missing values (`position_stack()`).
```

<img src="man/figures/README-africa-dataviz-4-africa-countries-1.png" width="100%" />

``` r
rc$ggplotComparisonExponentialGrowth(included.countries = africa.countries, min.cases = 20)
#> Warning: ggrepel: 41 unlabeled data points (too many overlaps). Consider
#> increasing max.overlaps
```

<img src="man/figures/README-africa-dataviz-4-africa-countries-2.png" width="100%" />

``` r

rg$ggplotCountriesLines(included.countries = africa.countries, countries.text = "Africa countries",
                        field = "confirmed.inc", log.scale = TRUE)
#> Warning: Removed 318 rows containing missing values (`geom_line()`).
#> Warning: ggrepel: 24 unlabeled data points (too many overlaps). Consider
#> increasing max.overlaps
```

<img src="man/figures/README-africa-dataviz-4-africa-countries-3.png" width="100%" />

``` r
rc$ggplotComparisonExponentialGrowth(included.countries = africa.countries, 
                                     field = "deaths", y.label = "deaths", min.cases = 1)
#> Warning: ggrepel: 37 unlabeled data points (too many overlaps). Consider
#> increasing max.overlaps
```

<img src="man/figures/README-africa-dataviz-4-africa-countries-4.png" width="100%" />

``` r
rg$ggplotTopCountriesStackedBarDailyInc(top.countries)
#> Warning: Removed 69 rows containing missing values (`position_stack()`).
```

<img src="man/figures/README-africa-dataviz-5-top-countries-1.png" width="100%" />

``` r
rc$ggplotComparisonExponentialGrowth(included.countries = international.countries, 
                                               min.cases = 100)
#> Warning: Removed 2 rows containing missing values (`geom_line()`).
#> Warning: ggrepel: 5 unlabeled data points (too many overlaps). Consider
#> increasing max.overlaps
```

<img src="man/figures/README-africa-dataviz-5-top-countries-2.png" width="100%" />

``` r
rg$ggplotCountriesLines(field = "confirmed.inc", log.scale = TRUE)
#> Warning: Removed 66 rows containing missing values (`geom_line()`).
```

<img src="man/figures/README-africa-dataviz-6-top-countries-inc-daily-1.png" width="100%" />

``` r
rg$ggplotCountriesLines(field = "rate.inc.daily", log.scale = TRUE)
#> Warning: Transformation introduced infinite values in continuous y-axis
#> Warning in self$trans$transform(x): NaNs produced
#> Warning: Transformation introduced infinite values in continuous y-axis
#> Warning in self$trans$transform(x): NaNs produced
#> Warning: Transformation introduced infinite values in continuous y-axis
#> Warning: Removed 119 rows containing missing values (`geom_line()`).
#> Warning: Removed 1 rows containing missing values (`geom_text_repel()`).
```

<img src="man/figures/README-africa-dataviz-6-top-countries-inc-daily-2.png" width="100%" />

``` r
rg$ggplotTopCountriesPie()
```

<img src="man/figures/README-africa-dataviz-7-top-countries-inc-legacy-1.png" width="100%" />

``` r
rg$ggplotTopCountriesBarPlots()
```

<img src="man/figures/README-africa-dataviz-7-top-countries-inc-legacy-2.png" width="100%" />

``` r
rg$ggplotCountriesBarGraphs(selected.country = "Ethiopia")
```

<img src="man/figures/README-africa-dataviz-7-top-countries-inc-legacy-3.png" width="100%" />
