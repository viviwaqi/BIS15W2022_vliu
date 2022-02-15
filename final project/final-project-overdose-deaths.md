---
title: "final project-overdose deaths"
author: "victoria liu"
date: "2/14/2022"
output: 
  html_document: 
    keep_md: yes
---
##Group Member Names:
John Abram Flores, Rajesvi, Tobin



```r
library(tidyverse)
library(janitor)
library(here)
library(naniar)
library(RColorBrewer)
library(paletteer)
library(janitor)
library(ggthemes)
```


```r
getwd()
```

```
## [1] "/Users/vcliu/Documents/GitHub/BIS15W2022_vliu/final project"
```


```r
overdoses<-read_csv("VSRR_Provisional_Drug_Overdose_Death_Counts.csv")
```

```
## Rows: 41625 Columns: 12
## -- Column specification --------------------------------------------------------
## Delimiter: ","
## chr (8): State, Month, Period, Indicator, Percent Complete, State Name, Foot...
## dbl (3): Year, Data Value, Percent Pending Investigation
## 
## i Use `spec()` to retrieve the full column specification for this data.
## i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
overdoses
```

```
## # A tibble: 41,625 x 12
##    State  Year Month Period          Indicator     `Data Value` `Percent Compl~`
##    <chr> <dbl> <chr> <chr>           <chr>                <dbl> <chr>           
##  1 AK     2015 April 12 month-ending Natural, sem~         NA   100             
##  2 AK     2015 April 12 month-ending Natural & se~         NA   100             
##  3 AK     2015 April 12 month-ending Natural & se~         NA   100             
##  4 AK     2015 April 12 month-ending Number of De~       4133   100             
##  5 AK     2015 April 12 month-ending Opioids (T40~         NA   100             
##  6 AK     2015 April 12 month-ending Cocaine (T40~         NA   100             
##  7 AK     2015 April 12 month-ending Synthetic op~         NA   100             
##  8 AK     2015 April 12 month-ending Psychostimul~         NA   100             
##  9 AK     2015 April 12 month-ending Heroin (T40.~         NA   100             
## 10 AK     2015 April 12 month-ending Percent with~         88.1 100             
## # ... with 41,615 more rows, and 5 more variables:
## #   `Percent Pending Investigation` <dbl>, `State Name` <chr>, Footnote <chr>,
## #   `Footnote Symbol` <chr>, `Predicted Value` <dbl>
```


```r
overdoses<- clean_names(overdoses)
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

```r
names(overdoses)
```

```
##  [1] "state"                         "year"                         
##  [3] "month"                         "period"                       
##  [5] "indicator"                     "data_value"                   
##  [7] "percent_complete"              "percent_pending_investigation"
##  [9] "state_name"                    "footnote"                     
## [11] "footnote_symbol"               "predicted_value"
```


