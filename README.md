
<!-- README.md is generated from README.Rmd. Please edit that file -->

<img src="man/figures/COVID19analytics.png" height="139" align="right" />

# COVID19analytics

<!-- . -->

This package curate (downloads, clean, consolidate, smooth) data from
[Johns Hopkins](https://github.com/CSSEGISandData/COVID-19/) and [Our
world in data](https://ourworldindata.org/coronavirus) for analysing
international outbreak of COVID-19.

It includes several visualizations of the COVID-19 international
outbreak.

- COVID19DataProcessor generates curated series
- [visualizations](https://www.r-bloggers.com/coronavirus-data-analysis-with-r-tidyverse-and-ggplot2/)
  by [Yanchang Zhao](https://www.r-bloggers.com/author/yanchang-zhao/)
  are included in ReportGenerator R6 object
- More visualizations included int ReportGeneratorEnhanced R6 object
- Visualizations ReportGeneratorDataComparison compares all countries
  counting epidemy day 0 when confirmed cases \> n (i.e. n = 100).

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

g First configurate environment variables with your preferred
configurations in `~/.Renviron`. COVID19analytics_data_dir is mandatory
while COVID19analytics_credits can be configured if you want to publish
your own research with space separated alias. Mention previous authors
where corresponding

``` .renviron
COVID19analytics_data_dir = "~/.R/COVID19analytics"
# If you want to generate your own reports
COVID19analytics_credits = "@alias1 @alias2 @aliasn"
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
library(knitr)
library(lgr)
```

``` r
log.dir <- file.path(getEnv("data_dir"), "logs")
dir.create(log.dir, recursive = TRUE, showWarnings = FALSE)
log.file <- file.path(log.dir, "covid19analytics.log")
lgr::get_logger("root")$add_appender(AppenderFile$new(log.file))
lgr::threshold("info", lgr::get_logger("root"))
lgr::threshold("info", lgr::get_logger("COVID19ARCurator"))
```

``` r
data.processor <- COVID19DataProcessor$new(provider = "JohnsHopkingsUniversity", missing.values = "imputation")

#dummy <- data.processor$preprocess() is setupData + transform is the preprocess made by data provider
dummy <- data.processor$setupData()
#> INFO  [08:59:15.363]  {stage: `processor-setup`}
#> INFO  [08:59:15.785] Checking required downloaded  {downloaded.max.date: `2022-10-21`, daily.update.time: `21:00:00`, current.datetime: `2022-10-23 08:59:15`, download.flag: `TRUE`}
#> INFO  [08:59:17.067] Checking required downloaded  {downloaded.max.date: `2022-10-21`, daily.update.time: `21:00:00`, current.datetime: `2022-10-23 08:59:16`, download.flag: `TRUE`}
#> INFO  [08:59:18.153] Checking required downloaded  {downloaded.max.date: `2022-10-21`, daily.update.time: `21:00:00`, current.datetime: `2022-10-23 08:59:18`, download.flag: `TRUE`}
#> INFO  [08:59:21.946]  {stage: `data loaded`}
#> INFO  [08:59:21.956]  {stage: `data-setup`}
dummy <- data.processor$transform()
#> INFO  [08:59:21.965] Executing transform
#> INFO  [08:59:21.969] Executing consolidate
#> INFO  [08:59:56.959]  {stage: `consolidated`}
#> INFO  [08:59:56.961] Executing standarize
#> INFO  [09:00:01.349] gathering DataModel
#> INFO  [09:00:01.351]  {stage: `datamodel-setup`}
# Curate is the process made by missing values method
dummy <- data.processor$curate()
#> INFO  [09:00:01.362]  {stage: `loading-aggregated-data-model`}
#> Warning in countrycode_convert(sourcevar = sourcevar, origin = origin, destination = dest, : Some values were not matched unambiguously: Antarctica
#> Warning in countrycode_convert(sourcevar = sourcevar, origin = origin, destination = dest, : Some values were not matched unambiguously: Micronesia
#> Warning in countrycode_convert(sourcevar = sourcevar, origin = origin, destination = dest, : Some values were not matched unambiguously: MS Zaandam
#> Warning in countrycode_convert(sourcevar = sourcevar, origin = origin, destination = dest, : Some values were not matched unambiguously: Summer Olympics 2020
#> Warning in countrycode_convert(sourcevar = sourcevar, origin = origin, destination = dest, : Some values were not matched unambiguously: Winter Olympics 2022
#> INFO  [09:00:07.347]  {stage: `calculating-rates`}
#> INFO  [09:00:07.731]  {stage: `making-data-comparison`}
#> INFO  [09:00:24.309]  {stage: `applying-missing-values-method`}
#> INFO  [09:00:24.311]  {stage: `Starting first imputation`}
#> INFO  [09:00:24.337]  {stage: `calculating-rates`}
#> INFO  [09:00:24.735]  {stage: `making-data-comparison-2`}
#> INFO  [09:00:41.662]  {stage: `calculating-top-countries`}
#> INFO  [09:00:41.702]  {stage: `curated`}

current.date <- max(data.processor$getData()$date)

rg <- ReportGeneratorEnhanced$new(data.processor)
rc <- ReportGeneratorDataComparison$new(data.processor = data.processor)

top.countries <- data.processor$top.countries
international.countries <- unique(c(data.processor$top.countries,
                                    "China", "Japan", "Singapore", "Korea, South"))
latam.countries <- sort(c("Mexico",
                     data.processor$countries$getCountries(division = "sub.continent", name = "Caribbean"),
                     data.processor$countries$getCountries(division = "sub.continent", name = "Central America"),
                     data.processor$countries$getCountries(division = "sub.continent", name = "South America")))
```

``` r
# Top 10 daily cases confirmed increment
kable((data.processor$getData() %>%
  filter(date == current.date) %>%
  select(country, date, rate.inc.daily, confirmed.inc, confirmed, deaths, deaths.inc) %>%
  arrange(desc(confirmed.inc)) %>%
  filter(confirmed >=10))[1:10,])
```

| country      | date       | rate.inc.daily | confirmed.inc | confirmed |  deaths | deaths.inc |
|:-------------|:-----------|---------------:|--------------:|----------:|--------:|-----------:|
| Taiwan\*     | 2022-10-22 |         0.0048 |         35624 |   7414829 |   12258 |         52 |
| Japan        | 2022-10-22 |         0.0016 |         34173 |  21989401 |   46336 |         78 |
| Italy        | 2022-10-22 |         0.0014 |         31775 |  23322522 |  178542 |         92 |
| Korea, South | 2022-10-22 |         0.0010 |         26256 |  25297334 |   28990 |         16 |
| Russia       | 2022-10-22 |         0.0004 |          9241 |  21060091 |  381582 |         88 |
| China        | 2022-10-22 |         0.0024 |          6920 |   2884383 |   15525 |          7 |
| Singapore    | 2022-10-22 |         0.0031 |          6339 |   2059709 |    1660 |          0 |
| Chile        | 2022-10-22 |         0.0013 |          5989 |   4708870 |   61535 |         22 |
| US           | 2022-10-22 |         0.0001 |          4971 |  97190530 | 1067685 |         12 |
| Malaysia     | 2022-10-22 |         0.0005 |          2618 |   4880005 |   36444 |          4 |

``` r
# Top 10 daily deaths increment
kable((data.processor$getData() %>%
  filter(date == current.date) %>%
  select(country, date, rate.inc.daily, confirmed.inc, confirmed, deaths, deaths.inc) %>%
  arrange(desc(deaths.inc)))[1:10,])
```

| country      | date       | rate.inc.daily | confirmed.inc | confirmed |  deaths | deaths.inc |
|:-------------|:-----------|---------------:|--------------:|----------:|--------:|-----------:|
| Italy        | 2022-10-22 |         0.0014 |         31775 |  23322522 |  178542 |         92 |
| Russia       | 2022-10-22 |         0.0004 |          9241 |  21060091 |  381582 |         88 |
| Japan        | 2022-10-22 |         0.0016 |         34173 |  21989401 |   46336 |         78 |
| Taiwan\*     | 2022-10-22 |         0.0048 |         35624 |   7414829 |   12258 |         52 |
| Philippines  | 2022-10-22 |         0.0004 |          1707 |   3993148 |   63742 |         36 |
| Chile        | 2022-10-22 |         0.0013 |          5989 |   4708870 |   61535 |         22 |
| Indonesia    | 2022-10-22 |         0.0003 |          2087 |   6469276 |  158416 |         18 |
| Korea, South | 2022-10-22 |         0.0010 |         26256 |  25297334 |   28990 |         16 |
| Poland       | 2022-10-22 |         0.0002 |          1115 |   6334706 |  118029 |         14 |
| US           | 2022-10-22 |         0.0001 |          4971 |  97190530 | 1067685 |         12 |

``` r
rg$ggplotTopCountriesStackedBarDailyInc(included.countries = latam.countries, countries.text = "Latam countries")
#> Warning: Removed 144 rows containing missing values (position_stack).
```

<img src="man/figures/README-dataviz-4-latam-1.png" width="100%" />

``` r
rc$ggplotComparisonExponentialGrowth(included.countries = latam.countries, countries.text = "Latam countries",   
                                     field = "confirmed", y.label = "Confirmed", min.cases = 100)
#> Warning: ggrepel: 7 unlabeled data points (too many overlaps). Consider
#> increasing max.overlaps
```

<img src="man/figures/README-dataviz-4-latam-2.png" width="100%" />

``` r
rc$ggplotComparisonExponentialGrowth(included.countries = latam.countries, countries.text = "Latam countries",   
                                     field = "remaining.confirmed", y.label = "Active cases", min.cases = 100)
#> Warning in self$trans$transform(x): NaNs produced
#> Warning: Transformation introduced infinite values in continuous y-axis
#> Warning: ggrepel: 7 unlabeled data points (too many overlaps). Consider
#> increasing max.overlaps
```

<img src="man/figures/README-dataviz-4-latam-3.png" width="100%" />

``` r
rc$ggplotComparisonExponentialGrowth(included.countries = latam.countries, field = "deaths", y.label = "Deaths", min.cases = 1)
```

<img src="man/figures/README-dataviz-4-latam-4.png" width="100%" />

``` r

rg$ggplotCrossSection(included.countries = latam.countries,
                       field.x = "confirmed",
                       field.y = "fatality.rate.max",
                       plot.description  = "Cross section Confirmed vs  Death rate min",
                       log.scale.x = TRUE,
                       log.scale.y = FALSE)
#> Warning: Removed 144 row(s) containing missing values (geom_path).
```

<img src="man/figures/README-dataviz-4-latam-5.png" width="100%" />

``` r

rg$ggplotCountriesLines(included.countries = latam.countries, countries.text = "Latam countries",
                        field = "confirmed.inc", log.scale = TRUE)
#> Warning: Removed 144 row(s) containing missing values (geom_path).
```

<img src="man/figures/README-dataviz-6-latam-inc-daily-1.png" width="100%" />

``` r
rg$ggplotCountriesLines(included.countries = latam.countries, countries.text = "Latam countries",
                        field = "deaths.inc", log.scale = TRUE)
#> Warning in self$trans$transform(x): NaNs produced
#> Warning: Transformation introduced infinite values in continuous y-axis
#> Warning in self$trans$transform(x): NaNs produced
#> Warning: Transformation introduced infinite values in continuous y-axis
#> Transformation introduced infinite values in continuous y-axis
#> Warning: Removed 9 rows containing missing values (geom_point).
#> Warning: Removed 144 row(s) containing missing values (geom_path).
```

<img src="man/figures/README-dataviz-6-latam-inc-daily-2.png" width="100%" />

``` r
rg$ggplotCountriesLines(included.countries = latam.countries, countries.text = "Latam countries",
                        field = "rate.inc.daily", log.scale = TRUE)
#> Warning: Transformation introduced infinite values in continuous y-axis
#> Warning in self$trans$transform(x): NaNs produced
#> Warning: Transformation introduced infinite values in continuous y-axis
#> Warning in self$trans$transform(x): NaNs produced
#> Warning: Transformation introduced infinite values in continuous y-axis
#> Warning: Removed 189 row(s) containing missing values (geom_path).
#> Warning: Removed 1 rows containing missing values (geom_text_repel).
#> Warning: ggrepel: 21 unlabeled data points (too many overlaps). Consider
#> increasing max.overlaps
```

<img src="man/figures/README-dataviz-6-latam-inc-daily-3.png" width="100%" />

``` r
rg$ggplotTopCountriesStackedBarDailyInc(top.countries)
#> Warning: Removed 69 rows containing missing values (position_stack).
```

<img src="man/figures/README-dataviz-7-top-countries-1.png" width="100%" />

``` r
rc$ggplotComparisonExponentialGrowth(included.countries = international.countries, 
                                     field = "confirmed", y.label = "Confirmed", min.cases = 100)
#> Warning: Removed 2 row(s) containing missing values (geom_path).
#> Warning: ggrepel: 2 unlabeled data points (too many overlaps). Consider
#> increasing max.overlaps
```

<img src="man/figures/README-dataviz-7-top-countries-2.png" width="100%" />

``` r
rc$ggplotComparisonExponentialGrowth(included.countries = international.countries, 
                                     field = "remaining.confirmed", y.label = "Active cases", min.cases = 100)
#> Warning: Removed 2 row(s) containing missing values (geom_path).
#> Warning: ggrepel: 4 unlabeled data points (too many overlaps). Consider
#> increasing max.overlaps
```

<img src="man/figures/README-dataviz-7-top-countries-3.png" width="100%" />

``` r
rc$ggplotComparisonExponentialGrowth(included.countries = international.countries, field = "deaths", 
                                     y.label = "Deaths", min.cases = 1)
#> Warning: Removed 2 row(s) containing missing values (geom_path).
```

<img src="man/figures/README-dataviz-7-top-countries-4.png" width="100%" />

``` r
rg$ggplotCrossSection(included.countries = international.countries,
                       field.x = "confirmed",
                       field.y = "fatality.rate.max",
                       plot.description  = "Cross section Confirmed vs Death rate min",
                       log.scale.x = TRUE,
                       log.scale.y = FALSE)
#> Warning: Removed 78 row(s) containing missing values (geom_path).
```

<img src="man/figures/README-dataviz-7-top-countries-5.png" width="100%" />

``` r
rg$ggplotCountriesLines(field = "confirmed.inc", log.scale = TRUE)
#> Warning: Removed 66 row(s) containing missing values (geom_path).
```

<img src="man/figures/README-dataviz-8-top-countries-inc-daily-1.png" width="100%" />

``` r
rg$ggplotCountriesLines(field = "deaths.inc", log.scale = TRUE)
#> Warning in self$trans$transform(x): NaNs produced
#> Warning: Transformation introduced infinite values in continuous y-axis
#> Transformation introduced infinite values in continuous y-axis
#> Warning: Removed 12 rows containing missing values (geom_point).
#> Warning: Removed 66 row(s) containing missing values (geom_path).
```

<img src="man/figures/README-dataviz-8-top-countries-inc-daily-2.png" width="100%" />

``` r
rg$ggplotCountriesLines(field = "rate.inc.daily", log.scale = TRUE)
#> Warning: Transformation introduced infinite values in continuous y-axis
#> Warning in self$trans$transform(x): NaNs produced
#> Warning: Transformation introduced infinite values in continuous y-axis
#> Warning in self$trans$transform(x): NaNs produced
#> Warning: Transformation introduced infinite values in continuous y-axis
#> Warning: Removed 67 row(s) containing missing values (geom_path).
#> Warning: Removed 1 rows containing missing values (geom_text_repel).
```

<img src="man/figures/README-dataviz-8-top-countries-inc-daily-3.png" width="100%" />

``` r
rg$ggplotTopCountriesPie()
```

<img src="man/figures/README-dataviz-9-top-countries-legacy-1.png" width="100%" />

``` r
rg$ggplotTopCountriesBarPlots()
```

<img src="man/figures/README-dataviz-9-top-countries-legacy-2.png" width="100%" />

``` r
rg$ggplotCountriesBarGraphs(selected.country = "Argentina")
```

<img src="man/figures/README-dataviz-9-top-countries-legacy-3.png" width="100%" />

# References

- Johns Hopkins University. Retrieved from:
  ‘<https://github.com/CSSEGISandData/COVID-19/>’ \[Online Resource\]

- OurWorldInData.org. Retrieved from:
  ‘<https://ourworldindata.org/coronavirus>’ \[Online Resource\]

Yanchang Zhao, COVID-19 Data Analysis with Tidyverse and Ggplot2 -
China. RDataMining.com, 2020.

URL:
<http://www.rdatamining.com/docs/Coronavirus-data-analysis-china.pdf>.
