---
title: "summarize practice, `count()`, `across()`"
date: "2022-01-19"
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
1. Produce clear, concise summaries using a variety of functions in `dplyr` and `janitor.`  
2. Use the `across()` operator to produce summaries across multiple variables.  

## Breakout Rooms  
Please take 5-8 minutes to check over your answers to the HW in your group. If you are stuck, please remember that you can check the key in [Joel's repository](https://github.com/jmledford3115/BIS15LW2021_jledford).  

## Load the libraries

```r
library("tidyverse")
library("janitor")
library("skimr")
library("palmerpenguins")
```

## Review
The summarize() and group_by() functions are powerful tools that we can use to produce clean summaries of data. Especially when used together, we can quickly group variables of interest and save time. Let's do some practice with the [palmerpenguins(https://allisonhorst.github.io/palmerpenguins/articles/intro.html) data to refresh our memory.

```r
glimpse(penguins)
```

```
## Rows: 344
## Columns: 8
## $ species           <fct> Adelie, Adelie, Adelie, Adelie, Adelie, Adelie, Adel~
## $ island            <fct> Torgersen, Torgersen, Torgersen, Torgersen, Torgerse~
## $ bill_length_mm    <dbl> 39.1, 39.5, 40.3, NA, 36.7, 39.3, 38.9, 39.2, 34.1, ~
## $ bill_depth_mm     <dbl> 18.7, 17.4, 18.0, NA, 19.3, 20.6, 17.8, 19.6, 18.1, ~
## $ flipper_length_mm <int> 181, 186, 195, NA, 193, 190, 181, 195, 193, 190, 186~
## $ body_mass_g       <int> 3750, 3800, 3250, NA, 3450, 3650, 3625, 4675, 3475, ~
## $ sex               <fct> male, female, female, NA, female, male, female, male~
## $ year              <int> 2007, 2007, 2007, 2007, 2007, 2007, 2007, 2007, 2007~
```

As biologists, a good question that we may ask is how do the measured variables differ by island (on average)?

```r
penguins %>% 
  group_by(island) %>% 
  summarize(mean_body_mass_g=mean(body_mass_g),
            n=n())
```

```
## # A tibble: 3 x 3
##   island    mean_body_mass_g     n
##   <fct>                <dbl> <int>
## 1 Biscoe                 NA    168
## 2 Dream                3713.   124
## 3 Torgersen              NA     52
```

Why do we have NA here? Do all of these penguins lack data?

```r
penguins %>% 
  group_by(island) %>% 
  summarize(number_NAs=sum(is.na(body_mass_g)))
```

```
## # A tibble: 3 x 2
##   island    number_NAs
##   <fct>          <int>
## 1 Biscoe             1
## 2 Dream              0
## 3 Torgersen          1
```

Well, that won't work so let's remove the NAs and recalculate.

```r
penguins %>% 
  filter(!is.na(body_mass_g)) %>% 
  group_by(island) %>% 
  summarize(mean_body_mass_g=mean(body_mass_g),
            sd_body_mass_g=sd(body_mass_g),
            n=n())
```

```
## # A tibble: 3 x 4
##   island    mean_body_mass_g sd_body_mass_g     n
##   <fct>                <dbl>          <dbl> <int>
## 1 Biscoe               4716.           783.   167
## 2 Dream                3713.           417.   124
## 3 Torgersen            3706.           445.    51
```

What if we are interested in the number of observations (penguins) by species and island?

```r
names(penguins)
```

```
## [1] "species"           "island"            "bill_length_mm"   
## [4] "bill_depth_mm"     "flipper_length_mm" "body_mass_g"      
## [7] "sex"               "year"
```


```r
penguins %>% 
  group_by(species, island) %>% 
  summarize(n=n(), .groups= 'keep')#the .groups argument here just prevents a warning message
```

```
## # A tibble: 5 x 3
## # Groups:   species, island [5]
##   species   island        n
##   <fct>     <fct>     <int>
## 1 Adelie    Biscoe       44
## 2 Adelie    Dream        56
## 3 Adelie    Torgersen    52
## 4 Chinstrap Dream        68
## 5 Gentoo    Biscoe      124
```

## Counts
Although these summary functions are super helpful, oftentimes we are mostly interested in counts. The [janitor package](https://garthtarr.github.io/meatR/janitor.html) does a lot with counts, but there are also functions that are part of dplyr that are useful.  

`count()` is an easy way of determining how many observations you have within a column. It acts like a combination of `group_by()` and `n()`.

```r
penguins %>% 
  count(island, sort = T) #sort=T sorts the column in descending order
```

```
## # A tibble: 3 x 2
##   island        n
##   <fct>     <int>
## 1 Biscoe      168
## 2 Dream       124
## 3 Torgersen    52
```

Compare this with `summarize()` and `group_by()`.

```r
penguins %>% 
  group_by(island) %>% 
  summarize(n=n())
```

```
## # A tibble: 3 x 2
##   island        n
##   <fct>     <int>
## 1 Biscoe      168
## 2 Dream       124
## 3 Torgersen    52
```

You can also use `count()` across multiple variables.

```r
penguins %>% 
  count(island, species, sort = T)
```

```
## # A tibble: 5 x 3
##   island    species       n
##   <fct>     <fct>     <int>
## 1 Biscoe    Gentoo      124
## 2 Dream     Chinstrap    68
## 3 Dream     Adelie       56
## 4 Torgersen Adelie       52
## 5 Biscoe    Adelie       44
```

For counts, I am starting to prefer `tabyl()`. Lots of options are supported in [tabyl](https://cran.r-project.org/web/packages/janitor/vignettes/tabyls.html)

```r
penguins %>% 
  tabyl(island, species)
```

```
##     island Adelie Chinstrap Gentoo
##     Biscoe     44         0    124
##      Dream     56        68      0
##  Torgersen     52         0      0
```


```r
penguins %>% 
  tabyl(species, island) %>% 
  adorn_percentages() %>%
  adorn_pct_formatting(digits = 2)
```

```
##    species  Biscoe   Dream Torgersen
##     Adelie  28.95%  36.84%    34.21%
##  Chinstrap   0.00% 100.00%     0.00%
##     Gentoo 100.00%   0.00%     0.00%
```

## Practice
1. Produce a summary of the mean for bill_length_mm, bill_depth_mm, flipper_length_mm, and body_mass_g within Adelie penguins only. Be sure to provide the number of samples.

```r
penguins %>%
  select(bill_length_mm, bill_depth_mm, flipper_length_mm, body_mass_g, species) %>%
  filter(species == "Adelie") %>%
  summarise(mean_bill_length=mean(bill_length_mm, na.rm = TRUE),
            mean_bill_depth=mean(bill_depth_mm, na.rm = TRUE),
            mean_flipper_length=mean(flipper_length_mm, na.rm = TRUE),
            mean_body_mass=mean(body_mass_g, na.rm = TRUE),
            n=n())
```

```
## # A tibble: 1 x 5
##   mean_bill_length mean_bill_depth mean_flipper_length mean_body_mass     n
##              <dbl>           <dbl>               <dbl>          <dbl> <int>
## 1             38.8            18.3                190.          3701.   152
```

2. How does the mean of `bill_length_mm` compare between penguin species?

```r
penguins %>%
  group_by(species) %>%
  summarise(species, mean_bill_each=mean(bill_length_mm, na.rm = TRUE))
```

```
## `summarise()` has grouped output by 'species'. You can override using the `.groups` argument.
```

```
## # A tibble: 344 x 2
## # Groups:   species [3]
##    species mean_bill_each
##    <fct>            <dbl>
##  1 Adelie            38.8
##  2 Adelie            38.8
##  3 Adelie            38.8
##  4 Adelie            38.8
##  5 Adelie            38.8
##  6 Adelie            38.8
##  7 Adelie            38.8
##  8 Adelie            38.8
##  9 Adelie            38.8
## 10 Adelie            38.8
## # ... with 334 more rows
```

3. For some penguins, their sex is listed as NA. Where do these penguins occur?

```r
penguins %>%
  select(species, sex) %>%
  filter(is.na(sex))
```

```
## # A tibble: 11 x 2
##    species sex  
##    <fct>   <fct>
##  1 Adelie  <NA> 
##  2 Adelie  <NA> 
##  3 Adelie  <NA> 
##  4 Adelie  <NA> 
##  5 Adelie  <NA> 
##  6 Adelie  <NA> 
##  7 Gentoo  <NA> 
##  8 Gentoo  <NA> 
##  9 Gentoo  <NA> 
## 10 Gentoo  <NA> 
## 11 Gentoo  <NA>
```

## `across()`
Last time we had some great questions on how to use `filter()` and `select()` across multiple variables. There is a new function in dplyr called `across()` which is designed to work across multiple variables. Have a look at Rebecca Barter's awesome blog [here](http://www.rebeccabarter.com/blog/2020-07-09-across/) as I am following her below.  

What if we wanted to apply summarize in order to produce distinct counts over multiple variables; i.e. species, island, and sex? Although this isn't a lot of coding you can image that with a lot of variables it would be cumbersome.

```r
penguins %>%
  summarize(distinct_species = n_distinct(species),
            distinct_island = n_distinct(island),
            distinct_sex = n_distinct(sex))
```

```
## # A tibble: 1 x 3
##   distinct_species distinct_island distinct_sex
##              <int>           <int>        <int>
## 1                3               3            3
```


```r
names(penguins)
```

```
## [1] "species"           "island"            "bill_length_mm"   
## [4] "bill_depth_mm"     "flipper_length_mm" "body_mass_g"      
## [7] "sex"               "year"
```

By using `across()` we can reduce the clutter and make things cleaner. 

```r
penguins %>%
  summarize(across(c(species, island, sex), n_distinct))
```

```
## # A tibble: 1 x 3
##   species island   sex
##     <int>  <int> <int>
## 1       3      3     3
```

This is very helpful for continuous variables.

```r
penguins %>%
  summarize(across(contains("mm"), mean, na.rm=T))
```

```
## # A tibble: 1 x 3
##   bill_length_mm bill_depth_mm flipper_length_mm
##            <dbl>         <dbl>             <dbl>
## 1           43.9          17.2              201.
```

`group_by` also works.

```r
table1 <- penguins %>%
  group_by(sex) %>% 
  summarize(across(contains("mm"), mean, na.rm=T))
```

Here we summarize across all variables.

```r
penguins %>%
  summarise_all(n_distinct)
```

```
## # A tibble: 1 x 8
##   species island bill_length_mm bill_depth_mm flipper_length_~ body_mass_g   sex
##     <int>  <int>          <int>         <int>            <int>       <int> <int>
## 1       3      3            165            81               56          95     3
## # ... with 1 more variable: year <int>
```

Operators can also work, here I am summarizing `n_distinct()` across all variables except `species`, `island`, and `sex`.

```r
penguins %>%
  summarise(across(!c(species, island, sex), 
                   n_distinct))
```

```
## # A tibble: 1 x 5
##   bill_length_mm bill_depth_mm flipper_length_mm body_mass_g  year
##            <int>         <int>             <int>       <int> <int>
## 1            165            81                56          95     3
```

All variables that include "bill"...all of the other dplyr operators also work.

```r
penguins %>%
  summarise(across(starts_with("bill"), n_distinct))
```

```
## # A tibble: 1 x 2
##   bill_length_mm bill_depth_mm
##            <int>         <int>
## 1            165            81
```

## Practice
1. Produce separate summaries of the mean and standard deviation for bill_length_mm, bill_depth_mm, and flipper_length_mm for each penguin species. Be sure to provide the number of samples.  
mean

```r
penguins %>%
  group_by(species) %>%
  summarise(across(c(bill_length_mm, bill_depth_mm, flipper_length_mm), mean, na.rm= TRUE), n=n())
```

```
## # A tibble: 3 x 5
##   species   bill_length_mm bill_depth_mm flipper_length_mm     n
##   <fct>              <dbl>         <dbl>             <dbl> <int>
## 1 Adelie              38.8          18.3              190.   152
## 2 Chinstrap           48.8          18.4              196.    68
## 3 Gentoo              47.5          15.0              217.   124
```

## Wrap-up  

Please review the learning goals and be sure to use the code here as a reference when completing the homework.  
-->[Home](https://jmledford3115.github.io/datascibiol/)
