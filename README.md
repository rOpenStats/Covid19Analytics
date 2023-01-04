
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
#> INFO  [08:36:56.966]  {stage: `processor-setup`}
#> INFO  [08:36:57.264] Checking required downloaded  {downloaded.max.date: `2023-01-01`, daily.update.time: `21:00:00`, current.datetime: `2023-01-03 08:36:57`, download.flag: `TRUE`}
#> INFO  [08:36:58.380] Checking required downloaded  {downloaded.max.date: `2023-01-01`, daily.update.time: `21:00:00`, current.datetime: `2023-01-03 08:36:58`, download.flag: `TRUE`}
#> INFO  [08:36:59.390] Checking required downloaded  {downloaded.max.date: `2023-01-01`, daily.update.time: `21:00:00`, current.datetime: `2023-01-03 08:36:59`, download.flag: `TRUE`}
#> INFO  [08:37:00.879]  {stage: `data loaded`}
#> INFO  [08:37:00.881]  {stage: `data-setup`}
dummy <- data.processor$transform()
#> INFO  [08:37:00.885] Executing transform
#> INFO  [08:37:00.886] Executing consolidate
#> INFO  [08:37:49.667]  {stage: `consolidated`}
#> INFO  [08:37:49.670] Executing standarize
#> INFO  [08:37:54.488] gathering DataModel
#> INFO  [08:37:54.491]  {stage: `datamodel-setup`}
# Curate is the process made by missing values method
dummy <- data.processor$curate()
#> INFO  [08:37:54.506]  {stage: `loading-aggregated-data-model`}
#> Warning in countrycode_convert(sourcevar = sourcevar, origin = origin, destination = dest, : Some values were not matched unambiguously: Antarctica
#> Warning in countrycode_convert(sourcevar = sourcevar, origin = origin, destination = dest, : Some values were not matched unambiguously: Micronesia
#> Warning in countrycode_convert(sourcevar = sourcevar, origin = origin, destination = dest, : Some values were not matched unambiguously: MS Zaandam
#> Warning in countrycode_convert(sourcevar = sourcevar, origin = origin, destination = dest, : Some values were not matched unambiguously: Summer Olympics 2020
#> Warning in countrycode_convert(sourcevar = sourcevar, origin = origin, destination = dest, : Some values were not matched unambiguously: Winter Olympics 2022
#> INFO  [08:38:01.220]  {stage: `calculating-rates`}
#> INFO  [08:38:01.572]  {stage: `making-data-comparison`}
#> INFO  [08:38:16.693]  {stage: `applying-missing-values-method`}
#> INFO  [08:38:16.696]  {stage: `Starting first imputation`}
#> INFO  [08:38:16.719]  {stage: `calculating-rates`}
#> INFO  [08:38:17.129]  {stage: `making-data-comparison-2`}
#> INFO  [09:27:10.579]  {stage: `calculating-top-countries`}
#> INFO  [09:27:10.669]  {stage: `curated`}

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
| Korea, South | 2023-01-02 |         0.0028 |         81056 |  29220591 |   32301 |         29 |
| Japan        | 2023-01-02 |         0.0026 |         76264 |  29397865 |   57729 |        208 |
| Argentina    | 2023-01-02 |         0.0073 |         72558 |   9963697 |  130171 |         47 |
| Germany      | 2023-01-02 |         0.0011 |         40783 |  37410650 |  161714 |        249 |
| France       | 2023-01-02 |         0.0010 |         39087 |  39537275 |  163255 |        252 |
| Brazil       | 2023-01-02 |         0.0009 |         31085 |  36362366 |  693981 |        128 |
| China        | 2023-01-02 |         0.0044 |         20230 |   4568666 |   17168 |         74 |
| Taiwan\*     | 2023-01-02 |         0.0019 |         16600 |   8889346 |   15299 |         26 |
| Uruguay      | 2023-01-02 |         0.0081 |          8173 |   1020161 |    7570 |          8 |
| US           | 2023-01-02 |         0.0001 |          6622 | 100759251 | 1092679 |          5 |

``` r
# Top 10 daily deaths increment
kable((data.processor$getData() %>%
  filter(date == current.date) %>%
  select(country, date, rate.inc.daily, confirmed.inc, confirmed, deaths, deaths.inc) %>%
  arrange(desc(deaths.inc)))[1:10,])
```

| country      | date       | rate.inc.daily | confirmed.inc | confirmed | deaths | deaths.inc |
|:-------------|:-----------|---------------:|--------------:|----------:|-------:|-----------:|
| France       | 2023-01-02 |         0.0010 |         39087 |  39537275 | 163255 |        252 |
| Germany      | 2023-01-02 |         0.0011 |         40783 |  37410650 | 161714 |        249 |
| Japan        | 2023-01-02 |         0.0026 |         76264 |  29397865 |  57729 |        208 |
| Brazil       | 2023-01-02 |         0.0009 |         31085 |  36362366 | 693981 |        128 |
| China        | 2023-01-02 |         0.0044 |         20230 |   4568666 |  17168 |         74 |
| Argentina    | 2023-01-02 |         0.0073 |         72558 |   9963697 | 130171 |         47 |
| Russia       | 2023-01-02 |         0.0002 |          3851 |  21499362 | 385881 |         42 |
| Romania      | 2023-01-02 |         0.0011 |          3605 |   3312085 |  67408 |         34 |
| Korea, South | 2023-01-02 |         0.0028 |         81056 |  29220591 |  32301 |         29 |
| Taiwan\*     | 2023-01-02 |         0.0019 |         16600 |   8889346 |  15299 |         26 |

``` r
rg$ggplotTopCountriesStackedBarDailyInc(included.countries = latam.countries, countries.text = "Latam countries")
#> Warning: Removed 144 rows containing missing values (`position_stack()`).
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
#> Warning: Removed 144 rows containing missing values (`geom_line()`).
#> Warning: ggrepel: 1 unlabeled data points (too many overlaps). Consider
#> increasing max.overlaps
```

<img src="man/figures/README-dataviz-4-latam-5.png" width="100%" />

``` r

rg$ggplotCountriesLines(included.countries = latam.countries, countries.text = "Latam countries",
                        field = "confirmed.inc", log.scale = TRUE)
#> Warning: Removed 144 rows containing missing values (`geom_line()`).
#> Warning: ggrepel: 2 unlabeled data points (too many overlaps). Consider
#> increasing max.overlaps
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
#> Warning: Removed 11 rows containing missing values (`geom_point()`).
#> Warning: Removed 144 rows containing missing values (`geom_line()`).
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
#> Warning: Removed 258 rows containing missing values (`geom_line()`).
#> Warning: Removed 1 rows containing missing values (`geom_text_repel()`).
#> Warning: ggrepel: 21 unlabeled data points (too many overlaps). Consider
#> increasing max.overlaps
```

<img src="man/figures/README-dataviz-6-latam-inc-daily-3.png" width="100%" />

``` r
rg$ggplotTopCountriesStackedBarDailyInc(top.countries)
#> Warning: Removed 69 rows containing missing values (`position_stack()`).
```

<img src="man/figures/README-dataviz-7-top-countries-1.png" width="100%" />

``` r
rc$ggplotComparisonExponentialGrowth(included.countries = international.countries, 
                                     field = "confirmed", y.label = "Confirmed", min.cases = 100)
#> Warning: Removed 2 rows containing missing values (`geom_line()`).
#> Warning: ggrepel: 6 unlabeled data points (too many overlaps). Consider
#> increasing max.overlaps
```

<img src="man/figures/README-dataviz-7-top-countries-2.png" width="100%" />

``` r
rc$ggplotComparisonExponentialGrowth(included.countries = international.countries, 
                                     field = "remaining.confirmed", y.label = "Active cases", min.cases = 100)
#> Warning: Removed 2 rows containing missing values (`geom_line()`).
#> Warning: ggrepel: 3 unlabeled data points (too many overlaps). Consider
#> increasing max.overlaps
```

<img src="man/figures/README-dataviz-7-top-countries-3.png" width="100%" />

``` r
rc$ggplotComparisonExponentialGrowth(included.countries = international.countries, field = "deaths", 
                                     y.label = "Deaths", min.cases = 1)
#> Warning: Removed 2 rows containing missing values (`geom_line()`).
```

<img src="man/figures/README-dataviz-7-top-countries-4.png" width="100%" />

``` r
rg$ggplotCrossSection(included.countries = international.countries,
                       field.x = "confirmed",
                       field.y = "fatality.rate.max",
                       plot.description  = "Cross section Confirmed vs Death rate min",
                       log.scale.x = TRUE,
                       log.scale.y = FALSE)
#> Warning: Removed 78 rows containing missing values (`geom_line()`).
```

<img src="man/figures/README-dataviz-7-top-countries-5.png" width="100%" />

``` r
rg$ggplotCountriesLines(field = "confirmed.inc", log.scale = TRUE)
#> Warning: Removed 66 rows containing missing values (`geom_line()`).
```

<img src="man/figures/README-dataviz-8-top-countries-inc-daily-1.png" width="100%" />

``` r
rg$ggplotCountriesLines(field = "deaths.inc", log.scale = TRUE)
#> Warning in self$trans$transform(x): NaNs produced
#> Warning: Transformation introduced infinite values in continuous y-axis
#> Transformation introduced infinite values in continuous y-axis
#> Warning: Removed 14 rows containing missing values (`geom_point()`).
#> Warning: Removed 66 rows containing missing values (`geom_line()`).
```

<img src="man/figures/README-dataviz-8-top-countries-inc-daily-2.png" width="100%" />

``` r
rg$ggplotCountriesLines(field = "rate.inc.daily", log.scale = TRUE)
#> Warning: Transformation introduced infinite values in continuous y-axis
#> Warning in self$trans$transform(x): NaNs produced
#> Warning: Transformation introduced infinite values in continuous y-axis
#> Warning in self$trans$transform(x): NaNs produced
#> Warning: Transformation introduced infinite values in continuous y-axis
#> Warning: Removed 111 rows containing missing values (`geom_line()`).
#> Warning: Removed 1 rows containing missing values (`geom_text_repel()`).
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
