---
title: "Lab 8 Homework"
author: "Victoria Liu"
date: "2022-02-03"
output:
  html_document: 
    theme: spacelab
    keep_md: yes
---



## Instructions
Answer the following questions and complete the exercises in RMarkdown. Please embed all of your code and push your final work to your repository. Your final lab report should be organized, clean, and run free from errors. Remember, you must remove the `#` for the included code chunks to run. Be sure to add your name to the author header above.  

Make sure to use the formatting conventions of RMarkdown to make your report neat and clean!  

## Load the libraries

```r
library(tidyverse)
library(janitor)
```

## Install `here`
The package `here` is a nice option for keeping directories clear when loading files. I will demonstrate below and let you decide if it is something you want to use.  

```r
#install.packages("here")
```

## Data
For this homework, we will use a data set compiled by the Office of Environment and Heritage in New South Whales, Australia. It contains the enterococci counts in water samples obtained from Sydney beaches as part of the Beachwatch Water Quality Program. Enterococci are bacteria common in the intestines of mammals; they are rarely present in clean water. So, enterococci values are a measurement of pollution. `cfu` stands for colony forming units and measures the number of viable bacteria in a sample [cfu](https://en.wikipedia.org/wiki/Colony-forming_unit).   

This homework loosely follows the tutorial of [R Ladies Sydney](https://rladiessydney.org/). If you get stuck, check it out!  

1. Start by loading the data `sydneybeaches`. Do some exploratory analysis to get an idea of the data structure.

```r
#sydneybeaches<- readr::read_csv("data/sydneybeaches.csv")
#sydneybeaches
```

If you want to try `here`, first notice the output when you load the `here` library. It gives you information on the current working directory. You can then use it to easily and intuitively load files.

```r
library(here)
```

```
## here() starts at /Users/vcliu/Documents/GitHub/BIS15W2022_vliu
```

The quotes show the folder structure from the root directory.

```r
sydneybeaches <-read_csv(here("lab8", "data", "sydneybeaches.csv")) %>% janitor::clean_names()
```

```
## Rows: 3690 Columns: 8
## -- Column specification --------------------------------------------------------
## Delimiter: ","
## chr (4): Region, Council, Site, Date
## dbl (4): BeachId, Longitude, Latitude, Enterococci (cfu/100ml)
## 
## i Use `spec()` to retrieve the full column specification for this data.
## i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```
## Warning in FUN(X[[i]], ...): unable to translate '<U+00C4>' to native encoding
```

```
## Warning in FUN(X[[i]], ...): unable to translate '<U+00D6>' to native encoding
```

```
## Warning in FUN(X[[i]], ...): unable to translate '<U+00DC>' to native encoding
```

```
## Warning in FUN(X[[i]], ...): unable to translate '<U+00E4>' to native encoding
```

```
## Warning in FUN(X[[i]], ...): unable to translate '<U+00F6>' to native encoding
```

```
## Warning in FUN(X[[i]], ...): unable to translate '<U+00FC>' to native encoding
```

```
## Warning in FUN(X[[i]], ...): unable to translate '<U+00DF>' to native encoding
```

```
## Warning in FUN(X[[i]], ...): unable to translate '<U+00C6>' to native encoding
```

```
## Warning in FUN(X[[i]], ...): unable to translate '<U+00E6>' to native encoding
```

```
## Warning in FUN(X[[i]], ...): unable to translate '<U+00D8>' to native encoding
```

```
## Warning in FUN(X[[i]], ...): unable to translate '<U+00F8>' to native encoding
```

```
## Warning in FUN(X[[i]], ...): unable to translate '<U+00C5>' to native encoding
```

```
## Warning in FUN(X[[i]], ...): unable to translate '<U+00E5>' to native encoding
```

2. Are these data "tidy" per the definitions of the tidyverse? How do you know? Are they in wide or long format?

```r
sydneybeaches
```

```
## # A tibble: 3,690 x 8
##    beach_id region       council site  longitude latitude date  enterococci_cfu~
##       <dbl> <chr>        <chr>   <chr>     <dbl>    <dbl> <chr>            <dbl>
##  1       25 Sydney City~ Randwi~ Clov~      151.    -33.9 02/0~               19
##  2       25 Sydney City~ Randwi~ Clov~      151.    -33.9 06/0~                3
##  3       25 Sydney City~ Randwi~ Clov~      151.    -33.9 12/0~                2
##  4       25 Sydney City~ Randwi~ Clov~      151.    -33.9 18/0~               13
##  5       25 Sydney City~ Randwi~ Clov~      151.    -33.9 30/0~                8
##  6       25 Sydney City~ Randwi~ Clov~      151.    -33.9 05/0~                7
##  7       25 Sydney City~ Randwi~ Clov~      151.    -33.9 11/0~               11
##  8       25 Sydney City~ Randwi~ Clov~      151.    -33.9 23/0~               97
##  9       25 Sydney City~ Randwi~ Clov~      151.    -33.9 07/0~                3
## 10       25 Sydney City~ Randwi~ Clov~      151.    -33.9 25/0~                0
## # ... with 3,680 more rows
```

```r
#long format, each variable has its own column, each observation has its own row, and each value has its own cell so it is tidy
```


3. We are only interested in the variables site, date, and enterococci_cfu_100ml. Make a new object focused on these variables only. Name the object `sydneybeaches_long`

```r
sydneybeaches_long<- sydneybeaches %>%
  select(site, date, enterococci_cfu_100ml)
sydneybeaches_long
```

```
## # A tibble: 3,690 x 3
##    site           date       enterococci_cfu_100ml
##    <chr>          <chr>                      <dbl>
##  1 Clovelly Beach 02/01/2013                    19
##  2 Clovelly Beach 06/01/2013                     3
##  3 Clovelly Beach 12/01/2013                     2
##  4 Clovelly Beach 18/01/2013                    13
##  5 Clovelly Beach 30/01/2013                     8
##  6 Clovelly Beach 05/02/2013                     7
##  7 Clovelly Beach 11/02/2013                    11
##  8 Clovelly Beach 23/02/2013                    97
##  9 Clovelly Beach 07/03/2013                     3
## 10 Clovelly Beach 25/03/2013                     0
## # ... with 3,680 more rows
```


4. Pivot the data such that the dates are column names and each beach only appears once. Name the object `sydneybeaches_wide`

```r
sydneybeaches_wide<- sydneybeaches_long %>%
  group_by(site) %>%
  pivot_wider(names_from = "date",
              values_from = "enterococci_cfu_100ml"
              )
sydneybeaches_wide
```

```
## # A tibble: 11 x 345
## # Groups:   site [11]
##    site         `02/01/2013` `06/01/2013` `12/01/2013` `18/01/2013` `30/01/2013`
##    <chr>               <dbl>        <dbl>        <dbl>        <dbl>        <dbl>
##  1 Clovelly Be~           19            3            2           13            8
##  2 Coogee Beach           15            4           17           18           22
##  3 Gordons Bay~           NA           NA           NA           NA           NA
##  4 Little Bay ~            9            3           72            1           44
##  5 Malabar Bea~            2            4          390           15           13
##  6 Maroubra Be~            1            1           20            2           11
##  7 South Marou~            1            0           33            2           13
##  8 South Marou~           12            2          110           13          100
##  9 Bondi Beach             3            1            2            1            6
## 10 Bronte Beach            4            2           38            3           25
## 11 Tamarama Be~            1            0            7           22           23
## # ... with 339 more variables: `05/02/2013` <dbl>, `11/02/2013` <dbl>,
## #   `23/02/2013` <dbl>, `07/03/2013` <dbl>, `25/03/2013` <dbl>,
## #   `02/04/2013` <dbl>, `12/04/2013` <dbl>, `18/04/2013` <dbl>,
## #   `24/04/2013` <dbl>, `01/05/2013` <dbl>, `20/05/2013` <dbl>,
## #   `31/05/2013` <dbl>, `06/06/2013` <dbl>, `12/06/2013` <dbl>,
## #   `24/06/2013` <dbl>, `06/07/2013` <dbl>, `18/07/2013` <dbl>,
## #   `24/07/2013` <dbl>, `08/08/2013` <dbl>, `22/08/2013` <dbl>, ...
```


5. Pivot the data back so that the dates are data and not column names.

```r
sydneybeaches_wide %>%
  pivot_longer(-site,
               names_to = "date",
               values_to = "enterococci",
               values_drop_na = TRUE)
```

```
## # A tibble: 3,661 x 3
## # Groups:   site [11]
##    site           date       enterococci
##    <chr>          <chr>            <dbl>
##  1 Clovelly Beach 02/01/2013          19
##  2 Clovelly Beach 06/01/2013           3
##  3 Clovelly Beach 12/01/2013           2
##  4 Clovelly Beach 18/01/2013          13
##  5 Clovelly Beach 30/01/2013           8
##  6 Clovelly Beach 05/02/2013           7
##  7 Clovelly Beach 11/02/2013          11
##  8 Clovelly Beach 23/02/2013          97
##  9 Clovelly Beach 07/03/2013           3
## 10 Clovelly Beach 25/03/2013           0
## # ... with 3,651 more rows
```


6. We haven't dealt much with dates yet, but separate the date into columns day, month, and year. Do this on the `sydneybeaches_long` data.

```r
sydneybeaches_long_sep<- sydneybeaches_long %>%
  separate(date, into = c("day", "month", "year"), sep = "/")
sydneybeaches_long_sep
```

```
## # A tibble: 3,690 x 5
##    site           day   month year  enterococci_cfu_100ml
##    <chr>          <chr> <chr> <chr>                 <dbl>
##  1 Clovelly Beach 02    01    2013                     19
##  2 Clovelly Beach 06    01    2013                      3
##  3 Clovelly Beach 12    01    2013                      2
##  4 Clovelly Beach 18    01    2013                     13
##  5 Clovelly Beach 30    01    2013                      8
##  6 Clovelly Beach 05    02    2013                      7
##  7 Clovelly Beach 11    02    2013                     11
##  8 Clovelly Beach 23    02    2013                     97
##  9 Clovelly Beach 07    03    2013                      3
## 10 Clovelly Beach 25    03    2013                      0
## # ... with 3,680 more rows
```


7. What is the average `enterococci_cfu_100ml` by year for each beach. Think about which data you will use- long or wide.

```r
sydneybeaches_long_sep<- sydneybeaches_long_sep %>%
  group_by(year, site) %>%
  mutate(ent_yearly_mean=mean(enterococci_cfu_100ml))
sydneybeaches_long_sep
```

```
## # A tibble: 3,690 x 6
## # Groups:   year, site [66]
##    site           day   month year  enterococci_cfu_100ml ent_yearly_mean
##    <chr>          <chr> <chr> <chr>                 <dbl>           <dbl>
##  1 Clovelly Beach 02    01    2013                     19            9.28
##  2 Clovelly Beach 06    01    2013                      3            9.28
##  3 Clovelly Beach 12    01    2013                      2            9.28
##  4 Clovelly Beach 18    01    2013                     13            9.28
##  5 Clovelly Beach 30    01    2013                      8            9.28
##  6 Clovelly Beach 05    02    2013                      7            9.28
##  7 Clovelly Beach 11    02    2013                     11            9.28
##  8 Clovelly Beach 23    02    2013                     97            9.28
##  9 Clovelly Beach 07    03    2013                      3            9.28
## 10 Clovelly Beach 25    03    2013                      0            9.28
## # ... with 3,680 more rows
```

8. Make the output from question 7 easier to read by pivoting it to wide format.

```r
sydneybeaches_long_sep %>%
  pivot_wider(names_from = "year",
              values_from = "ent_yearly_mean"
              )
```

```
## # A tibble: 3,589 x 10
## # Groups:   site [11]
##    site   day   month enterococci_cfu~ `2013` `2014` `2015` `2016` `2017` `2018`
##    <chr>  <chr> <chr>            <dbl>  <dbl>  <dbl>  <dbl>  <dbl>  <dbl>  <dbl>
##  1 Clove~ 02    01                  19   9.28     NA     NA     NA  NA        NA
##  2 Clove~ 06    01                   3   9.28     NA     NA     NA  NA        NA
##  3 Clove~ 12    01                   2   9.28     NA     NA     NA  NA        NA
##  4 Clove~ 18    01                  13   9.28     NA     NA     NA  NA        NA
##  5 Clove~ 30    01                   8   9.28     NA     NA     NA   7.93     NA
##  6 Clove~ 05    02                   7   9.28     NA     NA     NA  NA        NA
##  7 Clove~ 11    02                  11   9.28     NA     NA     NA  NA        NA
##  8 Clove~ 23    02                  97   9.28     NA     NA     NA  NA        NA
##  9 Clove~ 07    03                   3   9.28     NA     NA     NA  NA        NA
## 10 Clove~ 25    03                   0   9.28     NA     NA     NA  NA        NA
## # ... with 3,579 more rows
```


9. What was the most polluted beach in 2018?

```r
#for some reason only the "sydney_beaches_wide" data has 2018?? not sure what happened
sydney_wide_2018<- sydneybeaches_wide %>%
  pivot_longer(-site,
               names_to = "date",
               values_to = "enterococci",
               values_drop_na = TRUE) %>%
  separate(date, into = c("day", "month", "year"), sep = "/") %>%
  group_by(site) %>%
  mutate(ent_yearly_sum=sum(enterococci)) %>%
  filter(year == "2018")
sydney_wide_2018
```

```
## # A tibble: 523 x 6
## # Groups:   site [11]
##    site           day   month year  enterococci ent_yearly_sum
##    <chr>          <chr> <chr> <chr>       <dbl>          <dbl>
##  1 Clovelly Beach 19    09    2018            1           3413
##  2 Clovelly Beach 28    09    2018            2           3413
##  3 Clovelly Beach 05    10    2018           34           3413
##  4 Clovelly Beach 03    01    2018            1           3413
##  5 Clovelly Beach 09    01    2018           71           3413
##  6 Clovelly Beach 15    01    2018           30           3413
##  7 Clovelly Beach 25    01    2018            6           3413
##  8 Clovelly Beach 13    02    2018            2           3413
##  9 Clovelly Beach 20    03    2018            5           3413
## 10 Clovelly Beach 26    03    2018           23           3413
## # ... with 513 more rows
```

```r
sydney_wide_2018 %>%
  arrange(desc(ent_yearly_sum))
```

```
## # A tibble: 523 x 6
## # Groups:   site [11]
##    site          day   month year  enterococci ent_yearly_sum
##    <chr>         <chr> <chr> <chr>       <dbl>          <dbl>
##  1 Malabar Beach 19    09    2018            3          23227
##  2 Malabar Beach 28    09    2018           52          23227
##  3 Malabar Beach 05    10    2018           54          23227
##  4 Malabar Beach 03    01    2018           65          23227
##  5 Malabar Beach 09    01    2018          110          23227
##  6 Malabar Beach 15    01    2018            9          23227
##  7 Malabar Beach 25    01    2018            1          23227
##  8 Malabar Beach 13    02    2018          320          23227
##  9 Malabar Beach 20    03    2018           17          23227
## 10 Malabar Beach 26    03    2018           32          23227
## # ... with 513 more rows
```

```r
#malabar beach was the most polluted
```

10. Please complete the class project survey at: [BIS 15L Group Project](https://forms.gle/H2j69Z3ZtbLH3efW6)


## Push your final code to GitHub!
Please be sure that you check the `keep md` file in the knit preferences.   
