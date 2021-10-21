strings and factors
================
zy
10/21/2021

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
library(p8105.datasets)
```

``` r
string_vec=c("my","name","is","Jeff")
str_detect(string_vec, "jeff")
```

    ## [1] FALSE FALSE FALSE FALSE

``` r
string_vec=c(
  "I think we all rule for participating",
  "I think I have been caught",
  "I think this will be quite fun actually",
  "it will be fun, i think"
)
str_detect(string_vec, "^i think")
```

    ## [1] FALSE FALSE FALSE FALSE

``` r
string_vec = c(
  '7th inning stretch',
  '1st half soon to begin. Texas won the toss.',
  'she is 5 feet 4 inches tall',
  '3AM - cant sleep :('
  )
str_detect(string_vec,"[0-9][a-zA-Z]")
```

    ## [1]  TRUE  TRUE FALSE  TRUE

``` r
str_detect(string_vec,"711")
```

    ## [1] FALSE FALSE FALSE FALSE

``` r
string_vec=c(
  'The CI is [2,5]',
  ':-]',
  ':-[',
  'I foung the answer on pages[6-7]'
  )

str_detect(string_vec,"\\[")
```

    ## [1]  TRUE FALSE  TRUE  TRUE

``` r
factor_vec=factor(c("male","male","female","female"))

as.numeric(factor_vec)
```

    ## [1] 2 2 1 1

``` r
fct_relevel(factor_vec,"male")
```

    ## [1] male   male   female female
    ## Levels: male female

``` r
as.numeric(factor_vec)
```

    ## [1] 2 2 1 1

``` r
nsduh_url="http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

table_marj=
  read_html(nsduh_url)%>%
  html_table()%>%
  first()%>%
  slice(-1)
table_marj
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
marj_df=
  table_marj%>%
  select(-contains("P value"))%>%
  pivot_longer(
    -State,
    names_to="age_year",
    values_to="percent")%>%
  separate(age_year,into=c("age","year"),"\\(")%>%
  mutate(
    year=str_replace(year,"\\(",""),
    precent=str_replace(percent,"[a-c]$",""),
    percent=as.numeric(percent)
  )%>%
  filter(!State%in%c("Total U.S.","Northeast","Mideast","South","West"))
```

    ## Warning in mask$eval_all_mutate(quo): 强制改变过程中产生了NA

``` r
marj_df%>%
  filter(age=="12-17")%>%
  mutate(State=fct_reorder(State,percent))%>%
  ggplot(aes(x=State,y=percent,color=year))+
  geom_point()+
  theme(axis.text.x = element_text(angle = 90, hjust = 1))
```

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](strings-and-factors_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

``` r
data("rest_inspec")
```

``` r
rest_inspec%>%
  janitor::tabyl(boro,grade)
```

    ##           boro     A     B    C Not Yet Graded   P    Z   NA_
    ##          BRONX 13688  2801  701            200 163  351 16833
    ##       BROOKLYN 37449  6651 1684            702 416  977 51930
    ##      MANHATTAN 61608 10532 2689            765 508 1237 80615
    ##        Missing     4     0    0              0   0    0    13
    ##         QUEENS 35952  6492 1593            604 331  913 45816
    ##  STATEN ISLAND  5215   933  207             85  47  149  6730

``` r
rest_inspec=
  rest_inspec%>%
  filter(
    str_detect(grade,"[ABC]"),
    !(boro=="Missing"))%>%
  mutate(boro=str_to_title(boro))
```

``` r
rest_inspec%>%
  filter(str_detect(dba,"[Pp][Ii][Zz][Zz][Aa]"))%>%
  mutate(
    boro=fct_infreq(boro),
    boro=fct_recode(boro,"The City"="Manhattan")
  )%>%
  ggplot(aes(x=boro,fill=grade))+
  geom_bar()
```

![](strings-and-factors_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->
