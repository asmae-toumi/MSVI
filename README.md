
<!-- README.md is generated from README.Rmd. Please edit that file -->

# MSVI

<!-- badges: start -->

<!-- badges: end -->

Website: <https://asmae-toumi.github.io/MSVI/>

The goal of the MSVI package is to provide researchers and analysts with
health and socioeconomic data at the state, metropolitan or
county-level.

The MSVI package contains:

  - `ahrf`: data provided by the Area Health Resources Files (AHRF)
    which include the counts of health care professions at the
    county-level from over 50 data sources. (Source:
    <https://data.hrsa.gov/topics/health-workforce/ahrf>)
  - `cms`: data from the Centers for Medicare & Medicaid Services on
    health outcomes and utilization. (Source:
    <https://data.cms.gov/mapping-medicare-disparities>)
  - `county_health_rankings`: data provided by The County Health
    Rankings & Roadmaps program on health outcomes and health factors.
    (Source:
    <https://www.countyhealthrankings.org/explore-health-rankings/measures-data-sources/2020-measures>)
  - `definitive_hc`: data on typical bed capacity and average yearly bed
    utilization of hospitals across the United States provided by
    Definitive Healthcare. (Source:
    <https://coronavirus-resources.esri.com/datasets/>)
  - `svi_ranking`: the Centers for Disease Control and Prevention
    (CDC)’s Social Vulnerability Index (SVI) which ranks counties on
    15 social factors, including unemployment, minority status, and
    disability, and further groups them into four related themes. SVI
    was calculated for metropolitan statistical area’s and added to
    `svi_ranking.rds`. (Source for county-level data:
    <https://www.atsdr.cdc.gov/placeandhealth/svi/index.html>)

### Installation

You can install the development version from
[GitHub](https://github.com/) with:

``` r
# install.packages("devtools")
devtools::install_github("asmae-toumi/MSVI")
```

### AHRF

View raw clinician rate and clinician rate per 100,000 people at the
state and county level by type of clinician.

``` r
library(MSVI)
library(tidyverse)

ahrf %>% 
  filter(year_represented_by_variable == "2016") %>% 
  group_by(state, variable) %>% 
  count() 
#> # A tibble: 972 x 3
#> # Groups:   state, variable [972]
#>    state   variable                  n
#>    <chr>   <chr>                 <int>
#>  1 Alabama Dentist                  67
#>  2 Alabama DO, All                  67
#>  3 Alabama Family Medicine, DO      67
#>  4 Alabama Family Medicine, MD      67
#>  5 Alabama General Practice, DO     67
#>  6 Alabama General Practice, MD     67
#>  7 Alabama Internal Medicine, DO    67
#>  8 Alabama Internal Medicine, MD    67
#>  9 Alabama MD, All                  67
#> 10 Alabama Nurse Practitioner       67
#> # … with 962 more rows
```

### CMS

The CMS data contains all-cause hospitalizations, all-cause
readmissions, overall and composite PQI (Prevention Quality Index) and
all emergency visits by county and state.

``` r
cms %>% 
  filter(condition == "All Emergency Department Visits") %>% 
  group_by(county, ) %>% 
  summarize(total_ed = sum(analysis_value)) %>% 
  top_n(5)
#> # A tibble: 5 x 2
#>   county            total_ed
#>   <chr>                <dbl>
#> 1 Franklin County      17283
#> 2 Jackson County       16234
#> 3 Jefferson County     18103
#> 4 Lincoln County       15529
#> 5 Washington County    20706
```

### Social Vulnerability Index

CDC developed the SVI to “help public health officials and emergency
response planners identify and map the communities that will most likely
need support before, during, and after a hazardous event.”. It ranks
counties vulnerability by the following themes: socioeconomic status,
household Composition & disability, minority status & language, housing
type & transportation, and an overall ranking. (Source for county-level
data: <https://www.atsdr.cdc.gov/placeandhealth/svi/index.html>)

``` r
svi_ranking %>%
  slice_max(overall_ranking, n = 20) 
#> # A tibble: 20 x 7
#>     fips name  socioeconomic household_disab… minority_langua… housing_transpo…
#>    <dbl> <chr>         <dbl>            <dbl>            <dbl>            <dbl>
#>  1 48047 broo…         0.999            0.966            0.994            0.991
#>  2 46060 <NA>          0.990            0.725            0.866            0.990
#>  3 48127 dimm…         0.995            0.994            0.990            0.932
#>  4 48131 duva…         0.982            0.999            0.981            0.898
#>  5 35029 luna…         0.995            0.950            0.990            0.977
#>  6 48507 zava…         0.985            0.986            0.992            0.933
#>  7 35006 cibo…         0.957            0.968            0.909            0.996
#>  8 48377 pres…         0.994            0.997            0.998            0.703
#>  9  6025 impe…         0.985            0.776            0.998            0.987
#> 10 22039 evan…         0.983            0.978            0.826            0.969
#> 11 33100 <NA>          0.979            0.314            0.971            0.971
#> 12 48109 culb…         0.955            0.991            0.986            0.884
#> 13  5017 chic…         0.936            0.991            0.827            0.986
#> 14 41045 malh…         0.915            0.887            0.903            0.999
#> 15 22117 wash…         0.974            0.984            0.811            0.964
#> 16  1005 barb…         0.978            0.915            0.856            0.989
#> 17  4017 nava…         0.960            0.957            0.934            0.962
#> 18 28163 yazo…         0.981            0.942            0.935            0.940
#> 19  1105 perr…         0.988            0.956            0.624            0.994
#> 20 35380 <NA>          0.997            0.552            0.822            0.932
#> # … with 1 more variable: overall_ranking <dbl>
```

### Definitive healthcare

Data on typical bed capacity and average yearly bed utilization of
hospitals across the United States provided by Definitive Healthcare.
(Source: <https://coronavirus-resources.esri.com/datasets/>)

``` r
library(skimr)

definitive_hc %>% 
  group_by(state_name) %>% 
  summarize(mean_icu_beds = mean(num_icu_beds))
#> # A tibble: 53 x 2
#>    state_name           mean_icu_beds
#>    <chr>                        <dbl>
#>  1 Alabama                      13.6 
#>  2 Alaska                        6.89
#>  3 Arizona                      14.8 
#>  4 Arkansas                      8.54
#>  5 California                   18.5 
#>  6 Colorado                     12.0 
#>  7 Connecticut                  20.6 
#>  8 Delaware                     16.5 
#>  9 District of Columbia         29.1 
#> 10 Florida                      22.7 
#> # … with 43 more rows
```

### County health rankings

Data provided by The County Health Rankings & Roadmaps program on health
outcomes and health factors. (Source:
<https://www.countyhealthrankings.org/explore-health-rankings/measures-data-sources/2020-measures>)

``` r
county_health_rankings %>%
  select(state_abbreviation, housing_cost_burden = severe_housing_cost_burden_raw_value) %>% 
  group_by(state_abbreviation) %>% 
  slice_min(housing_cost_burden)
#> # A tibble: 51 x 2
#> # Groups:   state_abbreviation [51]
#>    state_abbreviation housing_cost_burden
#>    <chr>                            <dbl>
#>  1 AK                              0.0325
#>  2 AL                              0.0705
#>  3 AR                              0.0622
#>  4 AZ                              0.0359
#>  5 CA                              0.102 
#>  6 CO                              0.0475
#>  7 CT                              0.125 
#>  8 DC                              0.184 
#>  9 DE                              0.128 
#> 10 FL                              0.0775
#> # … with 41 more rows
```
