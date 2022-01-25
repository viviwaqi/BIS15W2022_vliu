---
title: "Midterm review and `naniar()`"
date: "2022-01-24"
output:
  html_document: 
    theme: spacelab
    toc: yes
    toc_float: yes
    keep_md: yes
  pdf_document:
    toc: yes
---

## Learning Goals
*At the end of this exercise, you will be able to:*    
1. Identify and manage NA's is data sets where NA's are represented in multiple ways.    
2. Use the `naniar` and `visdat` packages to help manage NA's in large data sets.  

## Breakout Rooms  
Please take 5-8 minutes to check over your answers to HW 6 in your group. If you are stuck, please remember that you can check the key in [Joel's repository](https://github.com/jmledford3115/BIS15LW2022_jledford).  

## Midterm 1 Review
Let's briefly review the questions from midterm 1 so you can get an idea of how I was thinking about the problems. Remember, there is more than one way to get at these answers, so don't worry if yours looks different than mine!  

## Load the libraries

```r
library("tidyverse")
library("janitor")
library("skimr")
```

## Review
When working with outside or "wild" data, dealing with NA's is a fundamental part of the data cleaning or tidying process. Data scientists spend most of their time cleaning and transforming data- including managing NA's. There isn't a single approach that will always work so you need to be careful about using replacements strategies across an entire data set. And, as the data sets become larger NA's can become trickier to deal with.  

For the following, we will use life history data for mammals. The [data](http://esapubs.org/archive/ecol/E084/093/) are from:  
**S. K. Morgan Ernest. 2003. Life history characteristics of placental non-volant mammals. Ecology 84:3402.**  

## Practice
1. Load the mammals life history data and clean the names.  

```r
life_history <- read_csv("data/mammal_lifehistories_v3.csv") %>% clean_names()
```

```
## Rows: 1440 Columns: 13
```

```
## -- Column specification --------------------------------------------------------
## Delimiter: ","
## chr (5): order, family, Genus, species, newborn
## dbl (8): mass, gestation, weaning, wean mass, AFR, max. life, litter size, l...
```

```
## 
## i Use `spec()` to retrieve the full column specification for this data.
## i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

2. Use one or more of the functions from the last lab to determine if there are NA's in the data, how they are represented, and where they are located.

```r
life_history
```

```
## # A tibble: 1,440 x 13
##    order  family genus species   mass gestation newborn weaning wean_mass    afr
##    <chr>  <chr>  <chr> <chr>    <dbl>     <dbl> <chr>     <dbl>     <dbl>  <dbl>
##  1 Artio~ Antil~ Anti~ americ~ 4.54e4      8.13 3246.36    3         8900   13.5
##  2 Artio~ Bovid~ Addax nasoma~ 1.82e5      9.39 5480       6.5       -999   27.3
##  3 Artio~ Bovid~ Aepy~ melamp~ 4.15e4      6.35 5093       5.63     15900   16.7
##  4 Artio~ Bovid~ Alce~ busela~ 1.5 e5      7.9  10166.~    6.5       -999   23.0
##  5 Artio~ Bovid~ Ammo~ clarkei 2.85e4      6.8  not me~ -999         -999 -999  
##  6 Artio~ Bovid~ Ammo~ lervia  5.55e4      5.08 3810       4         -999   14.9
##  7 Artio~ Bovid~ Anti~ marsup~ 3   e4      5.72 3910       4.04      -999   10.2
##  8 Artio~ Bovid~ Anti~ cervic~ 3.75e4      5.5  3846       2.13      -999   20.1
##  9 Artio~ Bovid~ Bison bison   4.98e5      8.93 20000     10.7     157500   29.4
## 10 Artio~ Bovid~ Bison bonasus 5   e5      9.14 23000.~    6.6       -999   30.0
## # ... with 1,430 more rows, and 3 more variables: max_life <dbl>,
## #   litter_size <dbl>, litters_year <dbl>
```


```r
anyNA(life_history)
```

```
## [1] TRUE
```


3. Can we use a single approach to deal with NA's in this data set? Given what you learned in the previous lab, how would you manage the NA values?

```r
#No, at least not just one function, there are NA values, -999, 0 and not measured values. I'm not sure I could put all those values into one "na_if" line of code, but I would use "na_if"
```

## `naniar`
`naniar` is a package that is built to manage NA's. Many of the functions it performs can also be performed using tidyverse functions, but it does provide some interesting alternatives.  

`miss_var_summary` provides a clean summary of NA's across the data frame.

```r
naniar::miss_var_summary(life_history)
```

```
## # A tibble: 13 x 3
##    variable     n_miss pct_miss
##    <chr>         <int>    <dbl>
##  1 litters_year    689     47.8
##  2 order             0      0  
##  3 family            0      0  
##  4 genus             0      0  
##  5 species           0      0  
##  6 mass              0      0  
##  7 gestation         0      0  
##  8 newborn           0      0  
##  9 weaning           0      0  
## 10 wean_mass         0      0  
## 11 afr               0      0  
## 12 max_life          0      0  
## 13 litter_size       0      0
```

Notice that `max_life` has no NA's. Does that make sense in the context of this data?

```r
hist(life_history$max_life)
```

![](lab7_2_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

```r
#yes it makes sense that there is no NA values because an animal has to live for at least a bit of time
```


```r
life_history <- 
  life_history %>% 
  mutate(max_life=na_if(max_life, 0))
```


```r
naniar::miss_var_summary(life_history)
```

```
## # A tibble: 13 x 3
##    variable     n_miss pct_miss
##    <chr>         <int>    <dbl>
##  1 max_life        841     58.4
##  2 litters_year    689     47.8
##  3 order             0      0  
##  4 family            0      0  
##  5 genus             0      0  
##  6 species           0      0  
##  7 mass              0      0  
##  8 gestation         0      0  
##  9 newborn           0      0  
## 10 weaning           0      0  
## 11 wean_mass         0      0  
## 12 afr               0      0  
## 13 litter_size       0      0
```

We can also use `miss_var_summary` with `group_by()`. This helps us better evaluate where NA's are in the data.

```r
life_history %>%
  group_by(order) %>%
  select(order, wean_mass) %>% 
  naniar::miss_var_summary(order=T)
```

```
## # A tibble: 17 x 4
## # Groups:   order [17]
##    order          variable  n_miss pct_miss
##    <chr>          <chr>      <int>    <dbl>
##  1 Artiodactyla   wean_mass      0        0
##  2 Carnivora      wean_mass      0        0
##  3 Cetacea        wean_mass      0        0
##  4 Dermoptera     wean_mass      0        0
##  5 Hyracoidea     wean_mass      0        0
##  6 Insectivora    wean_mass      0        0
##  7 Lagomorpha     wean_mass      0        0
##  8 Macroscelidea  wean_mass      0        0
##  9 Perissodactyla wean_mass      0        0
## 10 Pholidota      wean_mass      0        0
## 11 Primates       wean_mass      0        0
## 12 Proboscidea    wean_mass      0        0
## 13 Rodentia       wean_mass      0        0
## 14 Scandentia     wean_mass      0        0
## 15 Sirenia        wean_mass      0        0
## 16 Tubulidentata  wean_mass      0        0
## 17 Xenarthra      wean_mass      0        0
```

`naniar` also has a nice replace function which will allow you to precisely control which values you want replaced with NA's in each variable.

```r
life_history %>% 
  naniar::replace_with_na(replace = list(newborn = "not measured", weaning= -999, wean_mass= -999, afr= -999, max_life= 0, litter_size= -999, gestation= -999, mass= -999)) %>% 
  naniar::miss_var_summary()
```

```
## # A tibble: 13 x 3
##    variable     n_miss pct_miss
##    <chr>         <int>    <dbl>
##  1 wean_mass      1039    72.2 
##  2 max_life        841    58.4 
##  3 litters_year    689    47.8 
##  4 weaning         619    43.0 
##  5 afr             607    42.2 
##  6 newborn         595    41.3 
##  7 gestation       418    29.0 
##  8 mass             85     5.90
##  9 litter_size      84     5.83
## 10 order             0     0   
## 11 family            0     0   
## 12 genus             0     0   
## 13 species           0     0
```

## Practice
Let's practice evaluating NA's in a large data set. The data are compiled from [CITES](https://cites.org/eng). This is the international organization that tracks trade in endangered wildlife. You can find information about the data [here](https://www.kaggle.com/cites/cites-wildlife-trade-database).  

Some key information:  
[country codes](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)  

1. Import the data and do a little exploration. Be sure to clean the names if necessary.

```r
cites<- readr::read_csv("data/cites.csv")
```

```
## Rows: 67161 Columns: 16
```

```
## -- Column specification --------------------------------------------------------
## Delimiter: ","
## chr (13): App., Taxon, Class, Order, Family, Genus, Importer, Exporter, Orig...
## dbl  (3): Year, Importer reported quantity, Exporter reported quantity
```

```
## 
## i Use `spec()` to retrieve the full column specification for this data.
## i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
cites
```

```
## # A tibble: 67,161 x 16
##     Year App.  Taxon     Class   Order   Family  Genus  Importer Exporter Origin
##    <dbl> <chr> <chr>     <chr>   <chr>   <chr>   <chr>  <chr>    <chr>    <chr> 
##  1  2016 I     Aquila h~ Aves    Falcon~ Accipi~ Aquila TR       NL       CZ    
##  2  2016 I     Aquila h~ Aves    Falcon~ Accipi~ Aquila XV       RS       RS    
##  3  2016 I     Haliaeet~ Aves    Falcon~ Accipi~ Halia~ BE       NO       <NA>  
##  4  2016 I     Haliaeet~ Aves    Falcon~ Accipi~ Halia~ BE       NO       <NA>  
##  5  2016 I     Haliaeet~ Aves    Falcon~ Accipi~ Halia~ DK       IS       <NA>  
##  6  2016 I     Haliaeet~ Aves    Falcon~ Accipi~ Halia~ XV       RS       RS    
##  7  2016 I     Harpia h~ Aves    Falcon~ Accipi~ Harpia BR       FR       <NA>  
##  8  2016 I     Harpia h~ Aves    Falcon~ Accipi~ Harpia BR       FR       <NA>  
##  9  2016 I     Harpia h~ Aves    Falcon~ Accipi~ Harpia BR       FR       <NA>  
## 10  2016 I     Acipense~ Actino~ Acipen~ Acipen~ Acipe~ CH       DE       <NA>  
## # ... with 67,151 more rows, and 6 more variables:
## #   Importer reported quantity <dbl>, Exporter reported quantity <dbl>,
## #   Term <chr>, Unit <chr>, Purpose <chr>, Source <chr>
```

```r
cites<- clean_names(cites)
names(cites)
```

```
##  [1] "year"                       "app"                       
##  [3] "taxon"                      "class"                     
##  [5] "order"                      "family"                    
##  [7] "genus"                      "importer"                  
##  [9] "exporter"                   "origin"                    
## [11] "importer_reported_quantity" "exporter_reported_quantity"
## [13] "term"                       "unit"                      
## [15] "purpose"                    "source"
```

2. Use `naniar` to summarize the NA's in each variable.

```r
naniar::miss_var_summary(cites)
```

```
## # A tibble: 16 x 3
##    variable                   n_miss pct_miss
##    <chr>                       <int>    <dbl>
##  1 unit                        60759  90.5   
##  2 origin                      41518  61.8   
##  3 importer_reported_quantity  35295  52.6   
##  4 exporter_reported_quantity  23140  34.5   
##  5 class                       20224  30.1   
##  6 purpose                      6059   9.02  
##  7 genus                        1459   2.17  
##  8 exporter                      573   0.853 
##  9 source                        544   0.810 
## 10 family                        461   0.686 
## 11 importer                       71   0.106 
## 12 order                          57   0.0849
## 13 year                            0   0     
## 14 app                             0   0     
## 15 taxon                           0   0     
## 16 term                            0   0
```

3. Try using `group_by()` with `naniar`. Look specifically at class and `exporter_reported_quantity`. For which taxonomic classes do we have a high proportion of missing export data?

```r
cites %>%
  group_by(exporter_reported_quantity, class) %>%
  naniar::miss_var_summary() 
```

```
## # A tibble: 114,254 x 5
## # Groups:   class, exporter_reported_quantity [8,161]
##    class exporter_reported_quantity variable                   n_miss pct_miss
##    <chr>                      <dbl> <chr>                       <int>    <dbl>
##  1 Aves                           1 unit                         1205  99.7   
##  2 Aves                           1 importer_reported_quantity    996  82.4   
##  3 Aves                           1 origin                        524  43.3   
##  4 Aves                           1 genus                           4   0.331 
##  5 Aves                           1 family                          1   0.0827
##  6 Aves                           1 importer                        1   0.0827
##  7 Aves                           1 exporter                        1   0.0827
##  8 Aves                           1 purpose                         1   0.0827
##  9 Aves                           1 source                          1   0.0827
## 10 Aves                           1 year                            0   0     
## # ... with 114,244 more rows
```

## Visualizing NAs
There is another package `visdat` that can be used to visualize the proportion of different classes of data, including missing data. But, it is limited by size.

```r
library(visdat)
```


```r
vis_dat(life_history) #classes of data
```

![](lab7_2_files/figure-html/unnamed-chunk-17-1.png)<!-- -->


```r
vis_miss(life_history)
```

![](lab7_2_files/figure-html/unnamed-chunk-18-1.png)<!-- -->

## Dealing with NA's in advance
If you are sure that you know how NA's are treated in the data, then you can deal with them in advance using `na()` as part of the `readr` package.

```r
life_history_advance <- 
  readr::read_csv(file = "data/mammal_lifehistories_v3.csv", 
                  na = c("NA", " ", ".", "-999")) #all NA, blank spaces, .,and -999 are treated as NA
```

```
## Warning: One or more parsing issues, see `problems()` for details
```

```
## Rows: 1440 Columns: 13
```

```
## -- Column specification --------------------------------------------------------
## Delimiter: ","
## chr (5): order, family, Genus, species, newborn
## dbl (8): mass, gestation, weaning, wean mass, AFR, max. life, litter size, l...
```

```
## 
## i Use `spec()` to retrieve the full column specification for this data.
## i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```


```r
naniar::miss_var_summary(life_history_advance)
```

```
## # A tibble: 13 x 3
##    variable     n_miss pct_miss
##    <chr>         <int>    <dbl>
##  1 wean mass      1039    72.2 
##  2 litters/year    689    47.8 
##  3 weaning         619    43.0 
##  4 AFR             607    42.2 
##  5 gestation       418    29.0 
##  6 mass             85     5.90
##  7 litter size      84     5.83
##  8 order             0     0   
##  9 family            0     0   
## 10 Genus             0     0   
## 11 species           0     0   
## 12 newborn           0     0   
## 13 max. life         0     0
```

## Wrap-up  
Please review the learning goals and be sure to use the code here as a reference when completing the homework.  
-->[Home](https://jmledford3115.github.io/datascibiol/)
