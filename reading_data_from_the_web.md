reading_data_from_the_web
================
zy
10/19/2021

``` r
library(tidyverse)
```

    ## ─ Attaching packages ──────────────────── tidyverse 1.3.1 ─

    ## ✓ ggplot2 3.3.3     ✓ purrr   0.3.4
    ## ✓ tibble  3.1.1     ✓ dplyr   1.0.7
    ## ✓ tidyr   1.1.3     ✓ stringr 1.4.0
    ## ✓ readr   2.0.1     ✓ forcats 0.5.1

    ## ─ Conflicts ───────────────────── tidyverse_conflicts() ─
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(rvest)
```

    ## 
    ## Attaching package: 'rvest'

    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
library(httr)
```

``` r
url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"
```

``` r
drug_use_html=
  read_html(url)
drug_use_html
```

    ## {html_document}
    ## <html lang="en">
    ## [1] <head>\n<link rel="P3Pv1" href="http://www.samhsa.gov/w3c/p3p.xml">\n<tit ...
    ## [2] <body>\r\n\r\n<noscript>\r\n<p>Your browser's Javascript is off. Hyperlin ...

``` r
drug_use_df=
  drug_use_html%>%
  html_table()%>%
  first()%>%
  slice(-1)
drug_use_df
```

    ## # A tibble: 56 x 16
    ##    State      `12+(2013-2014)` `12+(2014-2015)` `12+(P Value)` `12-17(2013-2014…
    ##    <chr>      <chr>            <chr>            <chr>          <chr>            
    ##  1 Total U.S. 12.90a           13.36            0.002          13.28b           
    ##  2 Northeast  13.88a           14.66            0.005          13.98            
    ##  3 Midwest    12.40b           12.76            0.082          12.45            
    ##  4 South      11.24a           11.64            0.029          12.02            
    ##  5 West       15.27            15.62            0.262          15.53a           
    ##  6 Alabama    9.98             9.60             0.426          9.90             
    ##  7 Alaska     19.60a           21.92            0.010          17.30            
    ##  8 Arizona    13.69            13.12            0.364          15.12            
    ##  9 Arkansas   11.37            11.59            0.678          12.79            
    ## 10 California 14.49            15.25            0.103          15.03            
    ## # … with 46 more rows, and 11 more variables: 12-17(2014-2015) <chr>,
    ## #   12-17(P Value) <chr>, 18-25(2013-2014) <chr>, 18-25(2014-2015) <chr>,
    ## #   18-25(P Value) <chr>, 26+(2013-2014) <chr>, 26+(2014-2015) <chr>,
    ## #   26+(P Value) <chr>, 18+(2013-2014) <chr>, 18+(2014-2015) <chr>,
    ## #   18+(P Value) <chr>

``` r
sw_html = 
  read_html("https://www.imdb.com/list/ls070150896/")
```

``` r
sw_titles=
  sw_html%>%
  html_elements(".lister-item-header a")%>%
  html_text()

sw_revenue=
  sw_html%>%
  html_elements(".text-small:nth-child(7) span:nth-child(5)")%>%
  html_text()
```

``` r
water_df=
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.json")%>%
  content("text")%>%
  jsonlite::fromJSON()%>%
  as_tibble()
water_df
```

    ## # A tibble: 42 x 4
    ##    year  new_york_city_popul… nyc_consumption_million_… per_capita_gallons_per_…
    ##    <chr> <chr>                <chr>                     <chr>                   
    ##  1 1979  7102100              1512                      213                     
    ##  2 1980  7071639              1506                      213                     
    ##  3 1981  7089241              1309                      185                     
    ##  4 1982  7109105              1382                      194                     
    ##  5 1983  7181224              1424                      198                     
    ##  6 1984  7234514              1465                      203                     
    ##  7 1985  7274054              1326                      182                     
    ##  8 1986  7319246              1351                      185                     
    ##  9 1987  7342476              1447                      197                     
    ## 10 1988  7353719              1484                      202                     
    ## # … with 32 more rows

``` r
poke = 
  GET("http://pokeapi.co/api/v2/pokemon/1") %>%
  content()

poke$name
```

    ## [1] "bulbasaur"
