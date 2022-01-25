---
title: "Fun with NA's"
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
1. Define `NA` and describe how they are treated in R.  
2. Produce summaries of the number of NA's in a data set.   
3. Replace values with `NA` in a data set as appropriate.  

## Install the packages `naniar` and `visdat`
We will use these in part 2 of today's lab.

```r
#install.packages("naniar")
#install.packages("visdat")
```

## Load the libraries

```r
library("tidyverse")
```

```
## -- Attaching packages --------------------------------------- tidyverse 1.3.1 --
```

```
## v ggplot2 3.3.5     v purrr   0.3.4
## v tibble  3.1.6     v dplyr   1.0.7
## v tidyr   1.1.4     v stringr 1.4.0
## v readr   2.1.1     v forcats 0.5.1
```

```
## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

```r
library("naniar")
library("skimr")
```

```
## 
## Attaching package: 'skimr'
```

```
## The following object is masked from 'package:naniar':
## 
##     n_complete
```

```r
library("janitor")
```

```
## 
## Attaching package: 'janitor'
```

```
## The following objects are masked from 'package:stats':
## 
##     chisq.test, fisher.test
```

## Dealing with NA's
In almost all large data sets, there are missing observations. These can be tricky to deal with, partly because you first need to determine how missing values were treated in the original study. In a given data set, NA might represent values that are unrecorded because they are unknown or missing. But, they can also represent data that are not applicable.  

Scientists use different conventions in showing missing data; some use blank spaces, others use "-", etc. Unfortunately, sometimes **missing data is indicated with numbers like -999.0 or zero!**, though this can be required in some situations (for example raster data). Often, a combination of methods are used. It is up to the data analyst to find out how missing values are represented in the data set and to deal with them appropriately. Remember, if missing data are represented by values then R will treat them as such and you could be misled.  

## For the following examples, we will use the built-in `msleep` data

```r
?msleep
```

```
## starting httpd help server ... done
```

## Are there any NA's?  
The `summary()` function in base R will tally NA's by variable, but only for categorical/factor, numeric, integer, or logical columns. What about any NA values in the character columns?

```r
summary(msleep)
```

```
##      name              genus               vore              order          
##  Length:83          Length:83          Length:83          Length:83         
##  Class :character   Class :character   Class :character   Class :character  
##  Mode  :character   Mode  :character   Mode  :character   Mode  :character  
##                                                                             
##                                                                             
##                                                                             
##                                                                             
##  conservation        sleep_total      sleep_rem      sleep_cycle    
##  Length:83          Min.   : 1.90   Min.   :0.100   Min.   :0.1167  
##  Class :character   1st Qu.: 7.85   1st Qu.:0.900   1st Qu.:0.1833  
##  Mode  :character   Median :10.10   Median :1.500   Median :0.3333  
##                     Mean   :10.43   Mean   :1.875   Mean   :0.4396  
##                     3rd Qu.:13.75   3rd Qu.:2.400   3rd Qu.:0.5792  
##                     Max.   :19.90   Max.   :6.600   Max.   :1.5000  
##                                     NA's   :22      NA's   :51      
##      awake          brainwt            bodywt        
##  Min.   : 4.10   Min.   :0.00014   Min.   :   0.005  
##  1st Qu.:10.25   1st Qu.:0.00290   1st Qu.:   0.174  
##  Median :13.90   Median :0.01240   Median :   1.670  
##  Mean   :13.57   Mean   :0.28158   Mean   : 166.136  
##  3rd Qu.:16.15   3rd Qu.:0.12550   3rd Qu.:  41.750  
##  Max.   :22.10   Max.   :5.71200   Max.   :6654.000  
##                  NA's   :27
```

Let's next check to see if our data has any NA's by evaluating everything logically. We have done this before, but this is a good time to review. `is.na()` is a function that determines whether a value in a data frame is or is not an NA. This is evaluated as TRUE or FALSE. This means that R is looking in the data for NA, not for any values that might represent NA. To R, an NA is an empty cell or spot in a vector.  

Keep in mind, if the authors of the data represented NA's in another way, they will not be detected by R.  

```r
is.na(msleep)
```

```
##        name genus  vore order conservation sleep_total sleep_rem sleep_cycle
##  [1,] FALSE FALSE FALSE FALSE        FALSE       FALSE      TRUE        TRUE
##  [2,] FALSE FALSE FALSE FALSE         TRUE       FALSE     FALSE        TRUE
##  [3,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE        TRUE
##  [4,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE       FALSE
##  [5,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE       FALSE
##  [6,] FALSE FALSE FALSE FALSE         TRUE       FALSE     FALSE       FALSE
##  [7,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE       FALSE
##  [8,] FALSE FALSE  TRUE FALSE         TRUE       FALSE      TRUE        TRUE
##  [9,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE       FALSE
## [10,] FALSE FALSE FALSE FALSE        FALSE       FALSE      TRUE        TRUE
## [11,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE        TRUE
## [12,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE       FALSE
## [13,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE        TRUE
## [14,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE       FALSE
## [15,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE        TRUE
## [16,] FALSE FALSE FALSE FALSE         TRUE       FALSE     FALSE        TRUE
## [17,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE       FALSE
## [18,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE       FALSE
## [19,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE        TRUE
## [20,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE       FALSE
## [21,] FALSE FALSE FALSE FALSE        FALSE       FALSE      TRUE        TRUE
## [22,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE       FALSE
## [23,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE       FALSE
## [24,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE        TRUE
## [25,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE       FALSE
## [26,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE        TRUE
## [27,] FALSE FALSE FALSE FALSE         TRUE       FALSE      TRUE        TRUE
## [28,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE       FALSE
## [29,] FALSE FALSE FALSE FALSE         TRUE       FALSE     FALSE       FALSE
## [30,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE        TRUE
## [31,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE        TRUE
## [32,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE        TRUE
## [33,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE        TRUE
## [34,] FALSE FALSE FALSE FALSE         TRUE       FALSE     FALSE       FALSE
## [35,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE        TRUE
## [36,] FALSE FALSE FALSE FALSE        FALSE       FALSE      TRUE        TRUE
## [37,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE        TRUE
## [38,] FALSE FALSE FALSE FALSE         TRUE       FALSE     FALSE       FALSE
## [39,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE        TRUE
## [40,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE       FALSE
## [41,] FALSE FALSE FALSE FALSE         TRUE       FALSE      TRUE        TRUE
## [42,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE       FALSE
## [43,] FALSE FALSE FALSE FALSE         TRUE       FALSE     FALSE       FALSE
## [44,] FALSE FALSE FALSE FALSE        FALSE       FALSE      TRUE        TRUE
## [45,] FALSE FALSE FALSE FALSE         TRUE       FALSE      TRUE        TRUE
## [46,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE        TRUE
## [47,] FALSE FALSE FALSE FALSE        FALSE       FALSE      TRUE        TRUE
## [48,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE       FALSE
## [49,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE        TRUE
## [50,] FALSE FALSE FALSE FALSE         TRUE       FALSE     FALSE       FALSE
## [51,] FALSE FALSE FALSE FALSE        FALSE       FALSE      TRUE        TRUE
## [52,] FALSE FALSE FALSE FALSE        FALSE       FALSE      TRUE        TRUE
## [53,] FALSE FALSE FALSE FALSE        FALSE       FALSE      TRUE        TRUE
## [54,] FALSE FALSE FALSE FALSE         TRUE       FALSE     FALSE       FALSE
## [55,] FALSE FALSE  TRUE FALSE        FALSE       FALSE     FALSE        TRUE
## [56,] FALSE FALSE FALSE FALSE        FALSE       FALSE      TRUE        TRUE
## [57,] FALSE FALSE  TRUE FALSE         TRUE       FALSE      TRUE        TRUE
## [58,] FALSE FALSE  TRUE FALSE         TRUE       FALSE     FALSE        TRUE
## [59,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE        TRUE
## [60,] FALSE FALSE FALSE FALSE        FALSE       FALSE      TRUE        TRUE
## [61,] FALSE FALSE FALSE FALSE         TRUE       FALSE     FALSE        TRUE
## [62,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE        TRUE
## [63,] FALSE FALSE  TRUE FALSE        FALSE       FALSE     FALSE        TRUE
## [64,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE       FALSE
## [65,] FALSE FALSE FALSE FALSE         TRUE       FALSE      TRUE        TRUE
## [66,] FALSE FALSE FALSE FALSE         TRUE       FALSE     FALSE        TRUE
## [67,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE       FALSE
## [68,] FALSE FALSE FALSE FALSE         TRUE       FALSE     FALSE       FALSE
## [69,] FALSE FALSE  TRUE FALSE         TRUE       FALSE     FALSE        TRUE
## [70,] FALSE FALSE FALSE FALSE        FALSE       FALSE      TRUE        TRUE
## [71,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE       FALSE
## [72,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE        TRUE
## [73,] FALSE FALSE  TRUE FALSE         TRUE       FALSE     FALSE       FALSE
## [74,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE       FALSE
## [75,] FALSE FALSE FALSE FALSE         TRUE       FALSE      TRUE        TRUE
## [76,] FALSE FALSE FALSE FALSE         TRUE       FALSE      TRUE        TRUE
## [77,] FALSE FALSE FALSE FALSE        FALSE       FALSE     FALSE       FALSE
## [78,] FALSE FALSE FALSE FALSE         TRUE       FALSE     FALSE        TRUE
## [79,] FALSE FALSE FALSE FALSE         TRUE       FALSE     FALSE       FALSE
## [80,] FALSE FALSE FALSE FALSE         TRUE       FALSE      TRUE        TRUE
## [81,] FALSE FALSE FALSE FALSE         TRUE       FALSE     FALSE        TRUE
## [82,] FALSE FALSE FALSE FALSE         TRUE       FALSE      TRUE        TRUE
## [83,] FALSE FALSE FALSE FALSE         TRUE       FALSE     FALSE       FALSE
##       awake brainwt bodywt
##  [1,] FALSE    TRUE  FALSE
##  [2,] FALSE   FALSE  FALSE
##  [3,] FALSE    TRUE  FALSE
##  [4,] FALSE   FALSE  FALSE
##  [5,] FALSE   FALSE  FALSE
##  [6,] FALSE    TRUE  FALSE
##  [7,] FALSE    TRUE  FALSE
##  [8,] FALSE    TRUE  FALSE
##  [9,] FALSE   FALSE  FALSE
## [10,] FALSE   FALSE  FALSE
## [11,] FALSE   FALSE  FALSE
## [12,] FALSE   FALSE  FALSE
## [13,] FALSE    TRUE  FALSE
## [14,] FALSE   FALSE  FALSE
## [15,] FALSE   FALSE  FALSE
## [16,] FALSE   FALSE  FALSE
## [17,] FALSE   FALSE  FALSE
## [18,] FALSE   FALSE  FALSE
## [19,] FALSE   FALSE  FALSE
## [20,] FALSE   FALSE  FALSE
## [21,] FALSE   FALSE  FALSE
## [22,] FALSE   FALSE  FALSE
## [23,] FALSE   FALSE  FALSE
## [24,] FALSE   FALSE  FALSE
## [25,] FALSE   FALSE  FALSE
## [26,] FALSE   FALSE  FALSE
## [27,] FALSE    TRUE  FALSE
## [28,] FALSE   FALSE  FALSE
## [29,] FALSE   FALSE  FALSE
## [30,] FALSE    TRUE  FALSE
## [31,] FALSE    TRUE  FALSE
## [32,] FALSE   FALSE  FALSE
## [33,] FALSE   FALSE  FALSE
## [34,] FALSE   FALSE  FALSE
## [35,] FALSE    TRUE  FALSE
## [36,] FALSE   FALSE  FALSE
## [37,] FALSE    TRUE  FALSE
## [38,] FALSE   FALSE  FALSE
## [39,] FALSE    TRUE  FALSE
## [40,] FALSE   FALSE  FALSE
## [41,] FALSE    TRUE  FALSE
## [42,] FALSE   FALSE  FALSE
## [43,] FALSE   FALSE  FALSE
## [44,] FALSE    TRUE  FALSE
## [45,] FALSE   FALSE  FALSE
## [46,] FALSE    TRUE  FALSE
## [47,] FALSE    TRUE  FALSE
## [48,] FALSE   FALSE  FALSE
## [49,] FALSE   FALSE  FALSE
## [50,] FALSE   FALSE  FALSE
## [51,] FALSE    TRUE  FALSE
## [52,] FALSE   FALSE  FALSE
## [53,] FALSE    TRUE  FALSE
## [54,] FALSE   FALSE  FALSE
## [55,] FALSE   FALSE  FALSE
## [56,] FALSE    TRUE  FALSE
## [57,] FALSE    TRUE  FALSE
## [58,] FALSE   FALSE  FALSE
## [59,] FALSE    TRUE  FALSE
## [60,] FALSE    TRUE  FALSE
## [61,] FALSE    TRUE  FALSE
## [62,] FALSE   FALSE  FALSE
## [63,] FALSE   FALSE  FALSE
## [64,] FALSE   FALSE  FALSE
## [65,] FALSE    TRUE  FALSE
## [66,] FALSE   FALSE  FALSE
## [67,] FALSE   FALSE  FALSE
## [68,] FALSE   FALSE  FALSE
## [69,] FALSE   FALSE  FALSE
## [70,] FALSE   FALSE  FALSE
## [71,] FALSE   FALSE  FALSE
## [72,] FALSE    TRUE  FALSE
## [73,] FALSE   FALSE  FALSE
## [74,] FALSE   FALSE  FALSE
## [75,] FALSE   FALSE  FALSE
## [76,] FALSE    TRUE  FALSE
## [77,] FALSE   FALSE  FALSE
## [78,] FALSE   FALSE  FALSE
## [79,] FALSE   FALSE  FALSE
## [80,] FALSE    TRUE  FALSE
## [81,] FALSE   FALSE  FALSE
## [82,] FALSE   FALSE  FALSE
## [83,] FALSE   FALSE  FALSE
```

We also learned...

```r
any_na(msleep)
```

```
## [1] TRUE
```

What are we supposed to do with those? Unless you have a very small data frame, applying these functions is not helpful but we can use them in another way. Let's incorporate `is.na` into `summarize()`.

```r
msleep %>% 
  summarize(number_nas = sum(is.na(msleep)))
```

```
## # A tibble: 1 x 1
##   number_nas
##        <int>
## 1        136
```

This is better, but we still don't have any idea of where those NA's are in our data. With such limited information, it would be hard to determine if the issue was systemic or isolated to a single variable. In order to do this, we can apply `is.na()` to each variable of interest- which would be very slow!

```r
msleep %>% 
  summarize(number_nas = sum(is.na(conservation)))
```

```
## # A tibble: 1 x 1
##   number_nas
##        <int>
## 1         29
```

Here we summarize the number of NA's over all of the variables.

```r
msleep %>% 
  summarise_all(~(sum(is.na(.))))
```

```
## # A tibble: 1 x 11
##    name genus  vore order conservation sleep_total sleep_rem sleep_cycle awake
##   <int> <int> <int> <int>        <int>       <int>     <int>       <int> <int>
## 1     0     0     7     0           29           0        22          51     0
## # ... with 2 more variables: brainwt <int>, bodywt <int>
```

## NA's using `purr`
What if we are working with hundreds or thousands of variables?! In order to deal with this problem efficiently we can use another package in the tidyverse called `purrr`. We will talk more about `purr` later, but for now let's have a look!

This handy bit of code generates a new data frame that sums all NA's across variables. This does the same thing as above, but it is faster.  

```r
msleep %>%
  purrr::map_df(~ sum(is.na(.))) #map to a new data frame the sum results of the is.na function for all columns
```

```
## # A tibble: 1 x 11
##    name genus  vore order conservation sleep_total sleep_rem sleep_cycle awake
##   <int> <int> <int> <int>        <int>       <int>     <int>       <int> <int>
## 1     0     0     7     0           29           0        22          51     0
## # ... with 2 more variables: brainwt <int>, bodywt <int>
```

We can use `pivot_longer()` to make viewing this output easier. We are covering pivots next week.

```r
msleep %>% 
  purrr::map_df(~ sum(is.na(.))) %>% 
  pivot_longer(everything(),
    names_to= "variables",
    values_to = "num_nas") %>% 
  arrange(desc(num_nas))
```

```
## # A tibble: 11 x 2
##    variables    num_nas
##    <chr>          <int>
##  1 sleep_cycle       51
##  2 conservation      29
##  3 brainwt           27
##  4 sleep_rem         22
##  5 vore               7
##  6 name               0
##  7 genus              0
##  8 order              0
##  9 sleep_total        0
## 10 awake              0
## 11 bodywt             0
```

This is much better, but we need to be careful. R can have difficulty interpreting missing data. This is especially true for categorical variables. Always do a reality check if the output doesn't make sense to you. A quick check never hurts and can save you from wasting time in the future or from drawing false conclusions.  

You can explore a specific variable using `count()`. Remember this works the same way as `group_by()` and `summarize()`.

```r
msleep %>% 
  count(conservation)
```

```
## # A tibble: 7 x 2
##   conservation     n
##   <chr>        <int>
## 1 cd               2
## 2 domesticated    10
## 3 en               4
## 4 lc              27
## 5 nt               4
## 6 vu               7
## 7 <NA>            29
```

Adding the `sort=TRUE` option automatically makes a descending list. We confirm that there are 29 NA's within the conservation variable.

```r
msleep %>% 
  count(conservation, sort = TRUE)
```

```
## # A tibble: 7 x 2
##   conservation     n
##   <chr>        <int>
## 1 <NA>            29
## 2 lc              27
## 3 domesticated    10
## 4 vu               7
## 5 en               4
## 6 nt               4
## 7 cd               2
```

It is true that all of this is redundant, but you want to be able to run multiple checks on the data. Remember, just because your code runs without errors doesn't mean it is doing what you intended.  

## Practice  
For this practice we will use amniote life history data from: Myhrvold N, Baldridge E, Chan B, Sivam D, Freeman DL, Ernest SKM (2015). “An amniote life-history database to perform comparative analyses with birds, mammals, and reptiles.” _Ecology_, *96*, 3109.
doi: 10.1890/15-0846.1 (URL: https://doi.org/10.1890/15-0846.1).  

1. Load the `amniota` data below and determine whether or not NA's are present. If they are present, how are they represented?

```r
amniota <- readr::read_csv("data/amniota.csv")
```

```
## Rows: 21322 Columns: 36
```

```
## -- Column specification --------------------------------------------------------
## Delimiter: ","
## chr  (6): class, order, family, genus, species, common_name
## dbl (30): subspecies, female_maturity_d, litter_or_clutch_size_n, litters_or...
```

```
## 
## i Use `spec()` to retrieve the full column specification for this data.
## i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```


```r
anyNA(amniota)
```

```
## [1] FALSE
```

```r
amniota<- na_if(amniota, -999.00)
amniota
```

```
## # A tibble: 21,322 x 36
##    class order   family  genus  species subspecies common_name  female_maturity~
##    <chr> <chr>   <chr>   <chr>  <chr>        <dbl> <chr>                   <dbl>
##  1 Aves  Accipi~ Accipi~ Accip~ albogu~         NA Pied Goshawk              NA 
##  2 Aves  Accipi~ Accipi~ Accip~ badius          NA Shikra                   363.
##  3 Aves  Accipi~ Accipi~ Accip~ bicolor         NA Bicolored H~              NA 
##  4 Aves  Accipi~ Accipi~ Accip~ brachy~         NA New Britain~              NA 
##  5 Aves  Accipi~ Accipi~ Accip~ brevip~         NA Levant Spar~             363.
##  6 Aves  Accipi~ Accipi~ Accip~ castan~         NA Chestnut-fl~              NA 
##  7 Aves  Accipi~ Accipi~ Accip~ chilen~         NA Chilean Hawk              NA 
##  8 Aves  Accipi~ Accipi~ Accip~ chiono~         NA White-breas~             548.
##  9 Aves  Accipi~ Accipi~ Accip~ cirroc~         NA Collared Sp~              NA 
## 10 Aves  Accipi~ Accipi~ Accip~ cooper~         NA Cooper's Ha~             730 
## # ... with 21,312 more rows, and 28 more variables:
## #   litter_or_clutch_size_n <dbl>, litters_or_clutches_per_y <dbl>,
## #   adult_body_mass_g <dbl>, maximum_longevity_y <dbl>, gestation_d <dbl>,
## #   weaning_d <dbl>, birth_or_hatching_weight_g <dbl>, weaning_weight_g <dbl>,
## #   egg_mass_g <dbl>, incubation_d <dbl>, fledging_age_d <dbl>,
## #   longevity_y <dbl>, male_maturity_d <dbl>,
## #   inter_litter_or_interbirth_interval_y <dbl>, female_body_mass_g <dbl>, ...
```

```r
#The NAs are represented as -999
```

2. Use one of the tools explained above to summarize NA's in the data.

```r
amniota %>%
  purrr::map_df(~ sum(is.na(.)))
```

```
## # A tibble: 1 x 36
##   class order family genus species subspecies common_name female_maturity_d
##   <int> <int>  <int> <int>   <int>      <int>       <int>             <int>
## 1     0     0      0     0       0      21322        1641             17849
## # ... with 28 more variables: litter_or_clutch_size_n <int>,
## #   litters_or_clutches_per_y <int>, adult_body_mass_g <int>,
## #   maximum_longevity_y <int>, gestation_d <int>, weaning_d <int>,
## #   birth_or_hatching_weight_g <int>, weaning_weight_g <int>, egg_mass_g <int>,
## #   incubation_d <int>, fledging_age_d <int>, longevity_y <int>,
## #   male_maturity_d <int>, inter_litter_or_interbirth_interval_y <int>,
## #   female_body_mass_g <int>, male_body_mass_g <int>, ...
```


3. What's going on here? Try using the `hist` function on one or more of the continuous variables.


```r
hist(amniota$male_maturity_d)
```

![](lab7_1_files/figure-html/unnamed-chunk-17-1.png)<!-- -->

```r
hist(amniota$egg_mass_g)
```

![](lab7_1_files/figure-html/unnamed-chunk-17-2.png)<!-- -->

4. Given that we know that the output of #2 is wrong, what do you think is the best approach for dealing with NA's in the `amniote` data?  

```r
#I just used the na_if function to turn anything that had the value of -999 into an NA value. Otherwise it's too hard to filter out the -999 value everytime you want to do a function
```


## Replacing values with NA
If you are certain that NA's are represented by a specific value in the data, the best way to deal with them is to replace the value with NA. This is similar to a find & replace function in excel. Although you can do this when you import the data, in most cases you will need to (and should!) do some exploration first.  

_A note on excel:_ you may find yourself tempted to just open the .csv in excel and use find & replace. But, this isn't transparent; i.e. you are manipulating the data in a way that others can't see so this is not good practice in data science.   

Let's say we know that NA's are represented by "-999" in `amniota`. We can replace this value with NA across the data frame. You might be tempted to overwrite `amniota`, but in these cases I like to make a new object.

```r
amniota_tidy <- amniota %>% 
  na_if("-999")
```

Now let's run that function from problem #2 again to see how we did.

```r
amniota_tidy %>% 
  summarise_all(~(sum(is.na(.))))
```

```
## # A tibble: 1 x 36
##   class order family genus species subspecies common_name female_maturity_d
##   <int> <int>  <int> <int>   <int>      <int>       <int>             <int>
## 1     0     0      0     0       0      21322        1641             17849
## # ... with 28 more variables: litter_or_clutch_size_n <int>,
## #   litters_or_clutches_per_y <int>, adult_body_mass_g <int>,
## #   maximum_longevity_y <int>, gestation_d <int>, weaning_d <int>,
## #   birth_or_hatching_weight_g <int>, weaning_weight_g <int>, egg_mass_g <int>,
## #   incubation_d <int>, fledging_age_d <int>, longevity_y <int>,
## #   male_maturity_d <int>, inter_litter_or_interbirth_interval_y <int>,
## #   female_body_mass_g <int>, male_body_mass_g <int>, ...
```

Wow, that is a lot of missing data! Let's try `purr`. For a large data set like `amniota` this is very convenient.

```r
amniota_tidy %>% 
  purrr::map_df(~ sum(is.na(.))) %>% 
  pivot_longer(everything(),
    names_to= "variables",
    values_to = "num_nas") %>% 
  arrange(desc(num_nas))
```

```
## # A tibble: 36 x 2
##    variables                      num_nas
##    <chr>                            <int>
##  1 subspecies                       21322
##  2 female_body_mass_at_maturity_g   21318
##  3 female_svl_at_maturity_cm        21120
##  4 fledging_mass_g                  21111
##  5 male_svl_cm                      21040
##  6 no_sex_maturity_d                20860
##  7 egg_width_mm                     20727
##  8 egg_length_mm                    20702
##  9 weaning_weight_g                 20258
## 10 female_svl_cm                    20242
## # ... with 26 more rows
```

## Replacing NAs with `na_if()` and `mutate()`
On occasion, we may want to treat recorded data as NA. To do this, we need to use `na_if` and `mutate()`. Let's switch to the built-in `msleep` data to keep things clear.  


```r
names(msleep)
```

```
##  [1] "name"         "genus"        "vore"         "order"        "conservation"
##  [6] "sleep_total"  "sleep_rem"    "sleep_cycle"  "awake"        "brainwt"     
## [11] "bodywt"
```


Let's say we want to treat "domesticated" as NA in the conservation column. First, how many NA's are present in the variable `conservation`?

```r
msleep %>% 
  count(conservation, sort=T)
```

```
## # A tibble: 7 x 2
##   conservation     n
##   <chr>        <int>
## 1 <NA>            29
## 2 lc              27
## 3 domesticated    10
## 4 vu               7
## 5 en               4
## 6 nt               4
## 7 cd               2
```

Now, let's change all instances of `domesticated` to NA.

```r
msleep %>%  
  mutate(conservation_modified = na_if(conservation, "domesticated"))%>% 
  count(conservation_modified, sort = T) #previously there were only 29 NA's
```

```
## # A tibble: 6 x 2
##   conservation_modified     n
##   <chr>                 <int>
## 1 <NA>                     39
## 2 lc                       27
## 3 vu                        7
## 4 en                        4
## 5 nt                        4
## 6 cd                        2
```

## Practice
For the following examples, we will use the `life_history` data for mammals. The [data](http://esapubs.org/archive/ecol/E084/093/) are from:  
**S. K. Morgan Ernest. 2003. Life history characteristics of placental non-volant mammals. Ecology 84:3402.**  

1. Load the mammals life history data v2. Do you see any NA's?  

```r
mammal_hist <- readr::read_csv("data/mammal_lifehistories_v2.csv")
```

```
## Rows: 1440 Columns: 13
```

```
## -- Column specification --------------------------------------------------------
## Delimiter: ","
## chr (4): order, family, Genus, species
## dbl (9): mass, gestation, newborn, weaning, wean mass, AFR, max. life, litte...
```

```
## 
## i Use `spec()` to retrieve the full column specification for this data.
## i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
any_na(mammal_hist)
```

```
## [1] FALSE
```


2. Explore the data using any method that you prefer. `skimr` provides a nice output with histograms for numeric variables.  

```r
skim(mammal_hist)
```


Table: Data summary

|                         |            |
|:------------------------|:-----------|
|Name                     |mammal_hist |
|Number of rows           |1440        |
|Number of columns        |13          |
|_______________________  |            |
|Column type frequency:   |            |
|character                |4           |
|numeric                  |9           |
|________________________ |            |
|Group variables          |None        |


**Variable type: character**

|skim_variable | n_missing| complete_rate| min| max| empty| n_unique| whitespace|
|:-------------|---------:|-------------:|---:|---:|-----:|--------:|----------:|
|order         |         0|             1|   7|  14|     0|       17|          0|
|family        |         0|             1|   6|  15|     0|       96|          0|
|Genus         |         0|             1|   3|  16|     0|      618|          0|
|species       |         0|             1|   3|  17|     0|     1191|          0|


**Variable type: numeric**

|skim_variable | n_missing| complete_rate|      mean|         sd|   p0|  p25|     p50|     p75|       p100|hist                                     |
|:-------------|---------:|-------------:|---------:|----------:|----:|----:|-------:|-------:|----------:|:----------------------------------------|
|mass          |         0|             1| 383576.72| 5055162.92| -999|   50|  403.02| 7009.17| 1.4900e+08|▇▁▁▁▁ |
|gestation     |         0|             1|   -287.25|     455.36| -999| -999|    1.05|    4.50| 2.1460e+01|▃▁▁▁▇ |
|newborn       |         0|             1|   6703.15|   90912.52| -999| -999|    2.65|   98.00| 2.2500e+06|▇▁▁▁▁ |
|weaning       |         0|             1|   -427.17|     496.71| -999| -999|    0.73|    2.00| 4.8000e+01|▆▁▁▁▇ |
|wean mass     |         0|             1|  16048.93|  503650.17| -999| -999| -999.00|   10.00| 1.9075e+07|▇▁▁▁▁ |
|AFR           |         0|             1|   -408.12|     504.97| -999| -999|    2.50|   15.61| 2.1000e+02|▆▁▁▁▇ |
|max. life     |         0|             1|   -490.26|     615.30| -999| -999| -999.00|  147.25| 1.3680e+03|▇▁▅▁▁ |
|litter size   |         0|             1|    -55.63|     234.88| -999|    1|    2.27|    3.84| 1.4180e+01|▁▁▁▁▇ |
|litters/year  |         0|             1|   -477.14|     500.03| -999| -999|    0.38|    1.15| 7.5000e+00|▇▁▁▁▇ |


3. Replace the entries that represent NA's in the data using `na_if()`. Double check your work.

```r
mammal_hist_NA<- na_if(mammal_hist, -999)
anyNA(mammal_hist_NA)
```

```
## [1] TRUE
```


```r
is.na(mammal_hist_NA)
```

```
##         order family Genus species  mass gestation newborn weaning wean mass
##    [1,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##    [2,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##    [3,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##    [4,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##    [5,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##    [6,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##    [7,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##    [8,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##    [9,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##   [10,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [11,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [12,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [13,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##   [14,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##   [15,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE    TRUE    TRUE      TRUE
##   [16,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##   [17,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [18,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##   [19,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##   [20,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##   [21,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [22,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [23,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [24,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##   [25,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##   [26,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##   [27,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##   [28,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##   [29,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE   FALSE    TRUE      TRUE
##   [30,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##   [31,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##   [32,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##   [33,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##   [34,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [35,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [36,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [37,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##   [38,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [39,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##   [40,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##   [41,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##   [42,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##   [43,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##   [44,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##   [45,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##   [46,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##   [47,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##   [48,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##   [49,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [50,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##   [51,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE    TRUE   FALSE      TRUE
##   [52,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [53,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [54,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [55,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##   [56,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##   [57,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [58,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [59,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##   [60,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##   [61,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE      TRUE
##   [62,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##   [63,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##   [64,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##   [65,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##   [66,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##   [67,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##   [68,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##   [69,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [70,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [71,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##   [72,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##   [73,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##   [74,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [75,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [76,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##   [77,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##   [78,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [79,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##   [80,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [81,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##   [82,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##   [83,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##   [84,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##   [85,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##   [86,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [87,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##   [88,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##   [89,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##   [90,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [91,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##   [92,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##   [93,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##   [94,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##   [95,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##   [96,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##   [97,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##   [98,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##   [99,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [100,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [101,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [102,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [103,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [104,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [105,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE   FALSE   FALSE      TRUE
##  [106,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [107,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [108,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [109,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [110,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [111,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [112,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [113,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [114,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [115,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [116,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [117,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [118,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [119,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [120,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [121,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [122,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [123,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [124,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [125,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [126,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [127,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [128,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [129,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [130,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [131,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [132,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [133,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [134,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [135,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [136,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [137,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [138,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [139,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [140,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [141,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [142,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [143,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [144,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [145,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [146,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [147,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [148,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [149,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [150,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [151,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [152,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [153,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [154,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [155,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [156,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [157,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [158,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [159,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [160,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [161,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [162,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [163,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [164,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [165,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [166,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [167,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [168,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [169,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [170,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [171,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [172,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [173,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [174,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [175,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [176,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [177,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [178,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [179,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [180,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [181,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [182,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [183,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [184,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [185,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [186,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [187,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [188,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [189,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [190,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [191,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [192,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [193,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [194,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [195,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [196,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [197,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [198,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [199,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [200,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [201,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [202,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [203,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [204,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [205,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [206,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [207,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [208,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [209,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [210,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [211,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [212,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [213,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [214,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [215,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [216,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [217,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [218,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [219,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [220,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [221,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [222,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [223,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [224,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [225,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [226,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [227,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [228,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [229,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [230,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [231,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
##  [232,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [233,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [234,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [235,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE     FALSE
##  [236,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
##  [237,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [238,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [239,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [240,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [241,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [242,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [243,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [244,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [245,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [246,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [247,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [248,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [249,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [250,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [251,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [252,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [253,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [254,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [255,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [256,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [257,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [258,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [259,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE    TRUE    TRUE      TRUE
##  [260,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [261,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [262,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [263,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [264,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [265,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [266,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [267,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [268,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [269,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [270,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [271,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [272,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [273,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [274,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [275,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [276,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [277,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [278,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [279,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [280,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [281,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [282,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [283,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [284,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [285,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [286,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [287,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [288,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [289,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [290,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [291,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [292,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [293,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [294,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [295,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [296,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [297,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [298,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [299,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [300,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [301,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE   FALSE   FALSE      TRUE
##  [302,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [303,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [304,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [305,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [306,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [307,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [308,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [309,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [310,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [311,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [312,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [313,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [314,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [315,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [316,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [317,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [318,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [319,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [320,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [321,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [322,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [323,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [324,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [325,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [326,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [327,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [328,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [329,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [330,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [331,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [332,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [333,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [334,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [335,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [336,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [337,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [338,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [339,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [340,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [341,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [342,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [343,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [344,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [345,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE      TRUE
##  [346,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [347,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [348,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [349,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [350,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE      TRUE
##  [351,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [352,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [353,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [354,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [355,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [356,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [357,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
##  [358,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
##  [359,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [360,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [361,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [362,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [363,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [364,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [365,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [366,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [367,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [368,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE   FALSE    TRUE      TRUE
##  [369,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [370,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
##  [371,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [372,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [373,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [374,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [375,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [376,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [377,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [378,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [379,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [380,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [381,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [382,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [383,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [384,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [385,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [386,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [387,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [388,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [389,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [390,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [391,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [392,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [393,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [394,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [395,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [396,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [397,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [398,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [399,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [400,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [401,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [402,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [403,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [404,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE   FALSE    TRUE      TRUE
##  [405,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [406,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [407,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE    TRUE    TRUE      TRUE
##  [408,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [409,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [410,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [411,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [412,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [413,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [414,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [415,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE   FALSE   FALSE      TRUE
##  [416,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [417,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [418,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [419,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [420,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [421,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
##  [422,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
##  [423,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [424,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [425,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [426,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [427,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [428,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [429,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [430,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [431,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [432,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
##  [433,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE      TRUE
##  [434,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [435,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [436,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [437,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [438,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [439,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE     FALSE
##  [440,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [441,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [442,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [443,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [444,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE     FALSE
##  [445,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [446,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [447,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [448,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE     FALSE
##  [449,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE     FALSE
##  [450,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [451,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [452,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [453,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [454,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [455,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [456,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE      TRUE
##  [457,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [458,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
##  [459,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [460,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [461,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE     FALSE
##  [462,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
##  [463,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [464,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [465,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [466,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [467,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [468,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [469,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [470,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [471,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [472,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [473,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
##  [474,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [475,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [476,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [477,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [478,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
##  [479,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [480,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE     FALSE
##  [481,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [482,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [483,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [484,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
##  [485,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [486,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
##  [487,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
##  [488,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
##  [489,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE     FALSE
##  [490,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [491,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [492,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [493,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [494,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [495,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [496,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [497,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [498,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE      TRUE
##  [499,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [500,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
##  [501,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [502,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [503,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [504,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [505,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [506,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [507,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [508,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [509,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [510,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [511,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [512,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [513,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [514,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [515,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [516,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE    TRUE    TRUE      TRUE
##  [517,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [518,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [519,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [520,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [521,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [522,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [523,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [524,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [525,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [526,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [527,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [528,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [529,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [530,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [531,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [532,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [533,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [534,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [535,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [536,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [537,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [538,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [539,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [540,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [541,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [542,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [543,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE   FALSE    TRUE      TRUE
##  [544,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [545,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE      TRUE
##  [546,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [547,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE   FALSE   FALSE      TRUE
##  [548,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [549,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
##  [550,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE   FALSE   FALSE      TRUE
##  [551,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE   FALSE   FALSE      TRUE
##  [552,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [553,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [554,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
##  [555,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [556,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [557,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [558,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [559,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [560,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [561,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [562,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [563,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [564,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [565,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [566,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [567,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [568,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [569,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [570,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [571,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [572,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [573,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [574,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [575,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [576,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [577,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [578,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE     FALSE
##  [579,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [580,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE      TRUE
##  [581,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [582,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [583,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE     FALSE
##  [584,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [585,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [586,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE      TRUE
##  [587,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [588,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [589,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [590,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [591,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [592,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [593,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [594,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [595,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [596,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE     FALSE
##  [597,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [598,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [599,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [600,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [601,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [602,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [603,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [604,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [605,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [606,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [607,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [608,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [609,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
##  [610,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [611,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [612,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [613,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [614,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [615,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [616,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
##  [617,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [618,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [619,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
##  [620,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [621,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [622,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [623,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [624,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [625,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [626,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [627,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [628,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [629,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [630,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE      TRUE
##  [631,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE     FALSE
##  [632,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [633,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [634,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [635,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [636,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [637,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE   FALSE      TRUE
##  [638,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [639,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [640,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [641,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [642,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [643,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [644,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [645,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE      TRUE
##  [646,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [647,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [648,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [649,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [650,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [651,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [652,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [653,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [654,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [655,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [656,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [657,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [658,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [659,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE      TRUE
##  [660,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [661,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [662,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [663,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [664,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [665,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [666,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [667,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [668,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [669,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [670,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [671,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [672,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [673,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [674,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [675,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [676,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [677,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [678,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [679,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [680,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [681,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE      TRUE
##  [682,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [683,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [684,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
##  [685,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [686,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [687,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [688,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [689,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [690,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE      TRUE
##  [691,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [692,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [693,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [694,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [695,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [696,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [697,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [698,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [699,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [700,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [701,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [702,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [703,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [704,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [705,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [706,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [707,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [708,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [709,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
##  [710,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [711,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [712,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [713,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [714,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [715,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [716,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE      TRUE
##  [717,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [718,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [719,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [720,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [721,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [722,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [723,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [724,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [725,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [726,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [727,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [728,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [729,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [730,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [731,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [732,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [733,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [734,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
##  [735,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [736,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
##  [737,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [738,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [739,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [740,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [741,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [742,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [743,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [744,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [745,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [746,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [747,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [748,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [749,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [750,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [751,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [752,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE      TRUE
##  [753,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [754,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [755,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [756,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [757,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [758,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [759,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [760,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE     FALSE
##  [761,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [762,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE    TRUE   FALSE      TRUE
##  [763,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
##  [764,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [765,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [766,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [767,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [768,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [769,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [770,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [771,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [772,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [773,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [774,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [775,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [776,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [777,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [778,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [779,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [780,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [781,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [782,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [783,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [784,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [785,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [786,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [787,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [788,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [789,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [790,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [791,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [792,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [793,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [794,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [795,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [796,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [797,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
##  [798,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [799,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [800,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [801,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [802,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
##  [803,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [804,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
##  [805,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [806,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
##  [807,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE    TRUE    TRUE      TRUE
##  [808,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [809,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [810,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [811,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [812,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [813,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [814,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [815,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [816,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [817,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [818,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [819,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [820,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [821,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [822,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [823,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [824,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [825,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [826,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [827,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [828,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [829,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [830,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [831,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE     FALSE
##  [832,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
##  [833,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [834,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [835,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [836,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE     FALSE
##  [837,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [838,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [839,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [840,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [841,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [842,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
##  [843,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [844,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [845,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [846,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [847,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [848,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [849,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [850,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [851,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE     FALSE
##  [852,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [853,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [854,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [855,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [856,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
##  [857,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [858,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [859,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [860,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [861,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [862,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [863,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [864,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [865,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [866,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [867,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [868,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [869,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [870,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [871,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [872,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [873,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [874,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [875,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
##  [876,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [877,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [878,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [879,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [880,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [881,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [882,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [883,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [884,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [885,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE    TRUE    TRUE      TRUE
##  [886,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [887,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE     FALSE
##  [888,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [889,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [890,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [891,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [892,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE     FALSE
##  [893,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [894,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [895,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [896,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [897,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [898,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [899,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [900,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [901,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [902,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
##  [903,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [904,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [905,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [906,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
##  [907,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [908,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
##  [909,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [910,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [911,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [912,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [913,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [914,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [915,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [916,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [917,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [918,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [919,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [920,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [921,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [922,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [923,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [924,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [925,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
##  [926,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [927,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [928,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [929,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [930,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [931,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [932,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [933,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [934,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
##  [935,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [936,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [937,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [938,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE     FALSE
##  [939,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [940,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [941,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [942,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [943,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [944,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [945,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [946,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [947,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE   FALSE   FALSE     FALSE
##  [948,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [949,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [950,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE     FALSE
##  [951,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
##  [952,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
##  [953,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [954,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [955,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [956,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [957,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [958,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [959,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE   FALSE    TRUE      TRUE
##  [960,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [961,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [962,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [963,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [964,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [965,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE      TRUE
##  [966,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [967,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
##  [968,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [969,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [970,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [971,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE     FALSE
##  [972,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [973,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [974,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [975,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [976,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [977,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE   FALSE    TRUE     FALSE
##  [978,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE   FALSE      TRUE
##  [979,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [980,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [981,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [982,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [983,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [984,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [985,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE   FALSE      TRUE
##  [986,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [987,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [988,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
##  [989,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [990,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
##  [991,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [992,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
##  [993,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [994,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
##  [995,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
##  [996,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##  [997,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE     FALSE
##  [998,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE   FALSE    TRUE      TRUE
##  [999,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1000,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
## [1001,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1002,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1003,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE     FALSE
## [1004,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1005,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1006,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1007,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
## [1008,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE   FALSE    TRUE      TRUE
## [1009,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
## [1010,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1011,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1012,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
## [1013,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1014,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE     FALSE
## [1015,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1016,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE      TRUE
## [1017,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1018,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1019,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1020,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1021,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1022,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
## [1023,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE     FALSE
## [1024,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE     FALSE
## [1025,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1026,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
## [1027,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1028,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1029,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
## [1030,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1031,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE   FALSE   FALSE     FALSE
## [1032,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1033,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1034,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1035,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1036,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1037,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1038,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
## [1039,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1040,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1041,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE   FALSE    TRUE      TRUE
## [1042,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE   FALSE    TRUE      TRUE
## [1043,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE   FALSE    TRUE      TRUE
## [1044,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1045,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1046,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
## [1047,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1048,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1049,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE     FALSE
## [1050,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
## [1051,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1052,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1053,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1054,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1055,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE      TRUE
## [1056,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1057,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1058,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1059,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1060,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1061,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1062,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1063,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1064,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1065,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1066,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1067,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
## [1068,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1069,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1070,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1071,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
## [1072,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1073,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
## [1074,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
## [1075,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1076,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1077,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1078,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1079,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1080,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1081,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1082,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1083,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE     FALSE
## [1084,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1085,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
## [1086,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1087,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE      TRUE
## [1088,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1089,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1090,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1091,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1092,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1093,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1094,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1095,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1096,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1097,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
## [1098,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1099,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1100,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1101,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1102,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
## [1103,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1104,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
## [1105,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1106,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1107,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1108,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1109,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1110,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1111,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1112,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1113,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE     FALSE
## [1114,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1115,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1116,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1117,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1118,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1119,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1120,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
## [1121,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1122,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
## [1123,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1124,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE   FALSE    TRUE      TRUE
## [1125,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE   FALSE    TRUE      TRUE
## [1126,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1127,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1128,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1129,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
## [1130,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1131,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1132,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1133,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1134,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1135,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE     FALSE
## [1136,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1137,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
## [1138,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE   FALSE   FALSE      TRUE
## [1139,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
## [1140,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1141,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1142,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1143,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1144,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE     FALSE
## [1145,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1146,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
## [1147,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1148,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1149,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1150,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1151,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1152,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1153,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1154,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1155,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1156,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1157,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
## [1158,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE      TRUE
## [1159,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1160,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1161,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1162,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1163,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE   FALSE   FALSE     FALSE
## [1164,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1165,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1166,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1167,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1168,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1169,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1170,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1171,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE     FALSE
## [1172,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1173,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1174,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1175,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1176,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1177,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1178,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1179,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1180,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1181,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1182,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1183,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1184,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1185,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1186,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1187,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1188,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1189,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1190,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1191,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE     FALSE
## [1192,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1193,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1194,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1195,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
## [1196,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1197,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1198,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1199,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1200,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
## [1201,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1202,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1203,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1204,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1205,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1206,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1207,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1208,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1209,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1210,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1211,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1212,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE     FALSE
## [1213,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1214,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1215,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1216,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
## [1217,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
## [1218,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1219,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1220,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1221,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1222,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1223,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1224,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1225,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1226,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1227,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1228,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
## [1229,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1230,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
## [1231,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1232,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1233,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1234,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1235,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE   FALSE    TRUE      TRUE
## [1236,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1237,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE     FALSE
## [1238,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1239,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE     FALSE
## [1240,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1241,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE     FALSE
## [1242,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1243,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1244,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1245,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
## [1246,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
## [1247,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1248,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1249,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE      TRUE
## [1250,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1251,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1252,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1253,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1254,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1255,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1256,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1257,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1258,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1259,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE     FALSE
## [1260,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
## [1261,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1262,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
## [1263,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1264,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1265,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1266,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
## [1267,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
## [1268,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE     FALSE
## [1269,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1270,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1271,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1272,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1273,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1274,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1275,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1276,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1277,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE     FALSE
## [1278,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE     FALSE
## [1279,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1280,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
## [1281,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1282,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1283,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1284,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1285,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1286,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1287,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1288,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
## [1289,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1290,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1291,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1292,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
## [1293,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE    TRUE    TRUE      TRUE
## [1294,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1295,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1296,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1297,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1298,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1299,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1300,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1301,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1302,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1303,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
## [1304,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1305,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
## [1306,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE   FALSE    TRUE      TRUE
## [1307,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1308,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1309,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1310,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE   FALSE    TRUE      TRUE
## [1311,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1312,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1313,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1314,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1315,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1316,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1317,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE    TRUE    TRUE      TRUE
## [1318,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1319,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
## [1320,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
## [1321,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
## [1322,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
## [1323,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1324,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE   FALSE    TRUE      TRUE
## [1325,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1326,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1327,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE   FALSE   FALSE      TRUE
## [1328,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
## [1329,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1330,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1331,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1332,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1333,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1334,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1335,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1336,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
## [1337,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1338,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1339,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1340,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1341,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1342,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1343,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1344,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1345,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1346,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1347,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE   FALSE      TRUE
## [1348,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1349,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE    TRUE    TRUE      TRUE
## [1350,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1351,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1352,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1353,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1354,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1355,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1356,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE     FALSE
## [1357,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
## [1358,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE      TRUE
## [1359,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1360,] FALSE  FALSE FALSE   FALSE  TRUE      TRUE    TRUE    TRUE     FALSE
## [1361,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1362,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1363,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1364,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1365,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE     FALSE
## [1366,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1367,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1368,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1369,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1370,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1371,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1372,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE    TRUE   FALSE      TRUE
## [1373,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1374,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1375,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1376,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE     FALSE
## [1377,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1378,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1379,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1380,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE     FALSE
## [1381,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1382,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1383,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1384,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE     FALSE
## [1385,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1386,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1387,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
## [1388,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1389,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1390,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1391,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1392,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1393,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1394,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1395,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1396,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1397,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1398,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
## [1399,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1400,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1401,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
## [1402,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1403,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE    TRUE   FALSE      TRUE
## [1404,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
## [1405,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1406,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1407,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1408,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE    TRUE      TRUE
## [1409,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1410,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE   FALSE      TRUE
## [1411,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1412,] FALSE  FALSE FALSE   FALSE  TRUE     FALSE   FALSE   FALSE      TRUE
## [1413,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1414,] FALSE  FALSE FALSE   FALSE FALSE      TRUE   FALSE    TRUE      TRUE
## [1415,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1416,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1417,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1418,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1419,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1420,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1421,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1422,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1423,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1424,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1425,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1426,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1427,] FALSE  FALSE FALSE   FALSE FALSE      TRUE    TRUE    TRUE      TRUE
## [1428,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1429,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1430,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
## [1431,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1432,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE     FALSE
## [1433,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1434,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1435,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1436,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1437,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1438,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE   FALSE      TRUE
## [1439,] FALSE  FALSE FALSE   FALSE FALSE     FALSE   FALSE   FALSE      TRUE
## [1440,] FALSE  FALSE FALSE   FALSE FALSE     FALSE    TRUE    TRUE      TRUE
##           AFR max. life litter size litters/year
##    [1,] FALSE     FALSE       FALSE        FALSE
##    [2,] FALSE     FALSE       FALSE        FALSE
##    [3,] FALSE     FALSE       FALSE        FALSE
##    [4,] FALSE     FALSE       FALSE         TRUE
##    [5,]  TRUE      TRUE       FALSE         TRUE
##    [6,] FALSE     FALSE       FALSE        FALSE
##    [7,] FALSE     FALSE       FALSE         TRUE
##    [8,] FALSE     FALSE       FALSE        FALSE
##    [9,] FALSE     FALSE       FALSE        FALSE
##   [10,] FALSE     FALSE       FALSE        FALSE
##   [11,] FALSE     FALSE       FALSE        FALSE
##   [12,] FALSE     FALSE       FALSE        FALSE
##   [13,] FALSE     FALSE       FALSE        FALSE
##   [14,] FALSE     FALSE       FALSE        FALSE
##   [15,]  TRUE     FALSE       FALSE         TRUE
##   [16,]  TRUE     FALSE       FALSE        FALSE
##   [17,] FALSE     FALSE       FALSE        FALSE
##   [18,] FALSE     FALSE       FALSE        FALSE
##   [19,]  TRUE      TRUE       FALSE         TRUE
##   [20,] FALSE     FALSE       FALSE         TRUE
##   [21,] FALSE      TRUE       FALSE        FALSE
##   [22,] FALSE     FALSE       FALSE        FALSE
##   [23,] FALSE     FALSE       FALSE        FALSE
##   [24,]  TRUE      TRUE        TRUE         TRUE
##   [25,]  TRUE     FALSE        TRUE         TRUE
##   [26,]  TRUE      TRUE       FALSE         TRUE
##   [27,]  TRUE      TRUE       FALSE         TRUE
##   [28,]  TRUE      TRUE       FALSE         TRUE
##   [29,]  TRUE      TRUE       FALSE         TRUE
##   [30,] FALSE      TRUE       FALSE         TRUE
##   [31,] FALSE      TRUE       FALSE         TRUE
##   [32,] FALSE     FALSE       FALSE         TRUE
##   [33,]  TRUE      TRUE       FALSE        FALSE
##   [34,] FALSE     FALSE       FALSE        FALSE
##   [35,] FALSE     FALSE       FALSE         TRUE
##   [36,] FALSE      TRUE       FALSE        FALSE
##   [37,]  TRUE      TRUE       FALSE         TRUE
##   [38,] FALSE     FALSE       FALSE         TRUE
##   [39,] FALSE      TRUE       FALSE        FALSE
##   [40,] FALSE      TRUE        TRUE         TRUE
##   [41,]  TRUE      TRUE       FALSE         TRUE
##   [42,]  TRUE      TRUE       FALSE         TRUE
##   [43,] FALSE      TRUE       FALSE         TRUE
##   [44,] FALSE      TRUE       FALSE         TRUE
##   [45,] FALSE      TRUE       FALSE         TRUE
##   [46,] FALSE      TRUE       FALSE         TRUE
##   [47,] FALSE      TRUE       FALSE        FALSE
##   [48,] FALSE     FALSE       FALSE        FALSE
##   [49,] FALSE      TRUE       FALSE        FALSE
##   [50,] FALSE     FALSE       FALSE        FALSE
##   [51,] FALSE      TRUE       FALSE         TRUE
##   [52,] FALSE     FALSE       FALSE        FALSE
##   [53,] FALSE      TRUE       FALSE         TRUE
##   [54,] FALSE     FALSE       FALSE        FALSE
##   [55,] FALSE      TRUE        TRUE         TRUE
##   [56,]  TRUE      TRUE       FALSE         TRUE
##   [57,] FALSE     FALSE       FALSE         TRUE
##   [58,] FALSE     FALSE       FALSE        FALSE
##   [59,] FALSE     FALSE       FALSE        FALSE
##   [60,] FALSE     FALSE       FALSE         TRUE
##   [61,] FALSE      TRUE       FALSE         TRUE
##   [62,]  TRUE      TRUE       FALSE        FALSE
##   [63,] FALSE     FALSE       FALSE        FALSE
##   [64,] FALSE     FALSE       FALSE         TRUE
##   [65,]  TRUE      TRUE       FALSE        FALSE
##   [66,] FALSE      TRUE       FALSE        FALSE
##   [67,] FALSE      TRUE        TRUE         TRUE
##   [68,] FALSE     FALSE       FALSE         TRUE
##   [69,] FALSE     FALSE       FALSE        FALSE
##   [70,] FALSE     FALSE       FALSE         TRUE
##   [71,] FALSE      TRUE       FALSE         TRUE
##   [72,] FALSE      TRUE       FALSE        FALSE
##   [73,] FALSE     FALSE       FALSE        FALSE
##   [74,] FALSE     FALSE       FALSE        FALSE
##   [75,] FALSE     FALSE       FALSE        FALSE
##   [76,] FALSE      TRUE       FALSE         TRUE
##   [77,]  TRUE      TRUE       FALSE         TRUE
##   [78,] FALSE      TRUE       FALSE        FALSE
##   [79,] FALSE     FALSE       FALSE        FALSE
##   [80,] FALSE      TRUE       FALSE        FALSE
##   [81,] FALSE      TRUE       FALSE        FALSE
##   [82,]  TRUE      TRUE       FALSE         TRUE
##   [83,]  TRUE     FALSE       FALSE         TRUE
##   [84,] FALSE     FALSE       FALSE         TRUE
##   [85,] FALSE     FALSE       FALSE         TRUE
##   [86,] FALSE      TRUE       FALSE         TRUE
##   [87,]  TRUE      TRUE       FALSE         TRUE
##   [88,] FALSE     FALSE       FALSE         TRUE
##   [89,] FALSE     FALSE       FALSE        FALSE
##   [90,] FALSE      TRUE       FALSE        FALSE
##   [91,] FALSE     FALSE       FALSE        FALSE
##   [92,] FALSE      TRUE       FALSE         TRUE
##   [93,] FALSE     FALSE       FALSE        FALSE
##   [94,] FALSE     FALSE       FALSE        FALSE
##   [95,] FALSE      TRUE       FALSE         TRUE
##   [96,] FALSE     FALSE       FALSE         TRUE
##   [97,] FALSE     FALSE       FALSE         TRUE
##   [98,]  TRUE      TRUE       FALSE         TRUE
##   [99,] FALSE     FALSE       FALSE        FALSE
##  [100,] FALSE     FALSE       FALSE        FALSE
##  [101,] FALSE     FALSE       FALSE        FALSE
##  [102,] FALSE     FALSE       FALSE        FALSE
##  [103,] FALSE      TRUE       FALSE        FALSE
##  [104,] FALSE     FALSE       FALSE        FALSE
##  [105,] FALSE      TRUE       FALSE        FALSE
##  [106,] FALSE     FALSE       FALSE        FALSE
##  [107,]  TRUE      TRUE       FALSE         TRUE
##  [108,] FALSE      TRUE       FALSE         TRUE
##  [109,] FALSE     FALSE       FALSE        FALSE
##  [110,] FALSE     FALSE       FALSE        FALSE
##  [111,] FALSE     FALSE       FALSE        FALSE
##  [112,] FALSE     FALSE       FALSE         TRUE
##  [113,] FALSE     FALSE       FALSE         TRUE
##  [114,]  TRUE      TRUE       FALSE        FALSE
##  [115,] FALSE     FALSE       FALSE         TRUE
##  [116,]  TRUE      TRUE       FALSE        FALSE
##  [117,] FALSE     FALSE       FALSE         TRUE
##  [118,] FALSE     FALSE       FALSE        FALSE
##  [119,] FALSE     FALSE       FALSE        FALSE
##  [120,] FALSE     FALSE       FALSE        FALSE
##  [121,] FALSE     FALSE       FALSE        FALSE
##  [122,] FALSE     FALSE       FALSE        FALSE
##  [123,] FALSE     FALSE       FALSE        FALSE
##  [124,] FALSE     FALSE       FALSE        FALSE
##  [125,] FALSE     FALSE       FALSE         TRUE
##  [126,] FALSE     FALSE       FALSE        FALSE
##  [127,]  TRUE      TRUE       FALSE         TRUE
##  [128,]  TRUE      TRUE       FALSE         TRUE
##  [129,] FALSE     FALSE       FALSE         TRUE
##  [130,]  TRUE      TRUE       FALSE         TRUE
##  [131,] FALSE      TRUE       FALSE         TRUE
##  [132,] FALSE     FALSE       FALSE        FALSE
##  [133,] FALSE     FALSE       FALSE         TRUE
##  [134,] FALSE     FALSE       FALSE        FALSE
##  [135,] FALSE     FALSE       FALSE        FALSE
##  [136,] FALSE     FALSE       FALSE        FALSE
##  [137,] FALSE     FALSE       FALSE        FALSE
##  [138,]  TRUE      TRUE       FALSE         TRUE
##  [139,] FALSE     FALSE       FALSE         TRUE
##  [140,] FALSE     FALSE       FALSE        FALSE
##  [141,] FALSE     FALSE       FALSE        FALSE
##  [142,] FALSE     FALSE       FALSE        FALSE
##  [143,] FALSE     FALSE       FALSE        FALSE
##  [144,] FALSE     FALSE       FALSE        FALSE
##  [145,] FALSE      TRUE       FALSE         TRUE
##  [146,]  TRUE      TRUE       FALSE         TRUE
##  [147,] FALSE     FALSE       FALSE        FALSE
##  [148,] FALSE     FALSE       FALSE        FALSE
##  [149,] FALSE     FALSE       FALSE         TRUE
##  [150,] FALSE     FALSE       FALSE        FALSE
##  [151,] FALSE     FALSE       FALSE         TRUE
##  [152,] FALSE      TRUE       FALSE         TRUE
##  [153,]  TRUE      TRUE       FALSE         TRUE
##  [154,] FALSE     FALSE       FALSE        FALSE
##  [155,] FALSE      TRUE       FALSE         TRUE
##  [156,] FALSE     FALSE       FALSE        FALSE
##  [157,] FALSE     FALSE       FALSE         TRUE
##  [158,] FALSE     FALSE       FALSE        FALSE
##  [159,]  TRUE     FALSE       FALSE         TRUE
##  [160,] FALSE     FALSE       FALSE         TRUE
##  [161,] FALSE     FALSE       FALSE         TRUE
##  [162,] FALSE     FALSE       FALSE        FALSE
##  [163,]  TRUE     FALSE        TRUE         TRUE
##  [164,] FALSE      TRUE       FALSE         TRUE
##  [165,] FALSE     FALSE       FALSE         TRUE
##  [166,]  TRUE     FALSE       FALSE         TRUE
##  [167,] FALSE     FALSE       FALSE        FALSE
##  [168,] FALSE     FALSE       FALSE        FALSE
##  [169,] FALSE     FALSE       FALSE        FALSE
##  [170,] FALSE     FALSE       FALSE        FALSE
##  [171,] FALSE     FALSE       FALSE        FALSE
##  [172,] FALSE     FALSE       FALSE         TRUE
##  [173,] FALSE     FALSE       FALSE         TRUE
##  [174,] FALSE     FALSE       FALSE        FALSE
##  [175,] FALSE     FALSE       FALSE        FALSE
##  [176,] FALSE     FALSE       FALSE        FALSE
##  [177,] FALSE     FALSE       FALSE         TRUE
##  [178,]  TRUE      TRUE       FALSE         TRUE
##  [179,] FALSE      TRUE       FALSE        FALSE
##  [180,] FALSE     FALSE       FALSE        FALSE
##  [181,] FALSE      TRUE       FALSE        FALSE
##  [182,] FALSE      TRUE       FALSE        FALSE
##  [183,] FALSE      TRUE       FALSE         TRUE
##  [184,]  TRUE      TRUE       FALSE         TRUE
##  [185,] FALSE      TRUE       FALSE         TRUE
##  [186,]  TRUE      TRUE       FALSE         TRUE
##  [187,]  TRUE      TRUE       FALSE         TRUE
##  [188,]  TRUE      TRUE       FALSE         TRUE
##  [189,] FALSE      TRUE       FALSE         TRUE
##  [190,] FALSE     FALSE       FALSE        FALSE
##  [191,] FALSE     FALSE       FALSE        FALSE
##  [192,] FALSE     FALSE       FALSE        FALSE
##  [193,] FALSE     FALSE       FALSE        FALSE
##  [194,] FALSE     FALSE       FALSE         TRUE
##  [195,] FALSE     FALSE       FALSE         TRUE
##  [196,] FALSE     FALSE       FALSE        FALSE
##  [197,] FALSE     FALSE       FALSE        FALSE
##  [198,] FALSE     FALSE       FALSE        FALSE
##  [199,] FALSE      TRUE       FALSE        FALSE
##  [200,] FALSE     FALSE       FALSE        FALSE
##  [201,] FALSE     FALSE       FALSE         TRUE
##  [202,]  TRUE      TRUE       FALSE         TRUE
##  [203,] FALSE     FALSE       FALSE        FALSE
##  [204,] FALSE     FALSE       FALSE        FALSE
##  [205,]  TRUE     FALSE       FALSE         TRUE
##  [206,] FALSE     FALSE       FALSE        FALSE
##  [207,] FALSE     FALSE       FALSE        FALSE
##  [208,] FALSE     FALSE       FALSE        FALSE
##  [209,] FALSE     FALSE       FALSE         TRUE
##  [210,]  TRUE      TRUE       FALSE         TRUE
##  [211,]  TRUE     FALSE       FALSE        FALSE
##  [212,] FALSE      TRUE       FALSE         TRUE
##  [213,] FALSE     FALSE       FALSE        FALSE
##  [214,] FALSE     FALSE       FALSE        FALSE
##  [215,] FALSE     FALSE       FALSE        FALSE
##  [216,] FALSE     FALSE       FALSE        FALSE
##  [217,]  TRUE      TRUE       FALSE         TRUE
##  [218,]  TRUE      TRUE       FALSE         TRUE
##  [219,]  TRUE     FALSE       FALSE         TRUE
##  [220,] FALSE     FALSE       FALSE         TRUE
##  [221,]  TRUE      TRUE       FALSE         TRUE
##  [222,] FALSE     FALSE       FALSE        FALSE
##  [223,] FALSE     FALSE       FALSE        FALSE
##  [224,]  TRUE     FALSE       FALSE         TRUE
##  [225,]  TRUE      TRUE       FALSE         TRUE
##  [226,] FALSE     FALSE       FALSE        FALSE
##  [227,] FALSE     FALSE       FALSE        FALSE
##  [228,]  TRUE      TRUE       FALSE         TRUE
##  [229,]  TRUE      TRUE       FALSE        FALSE
##  [230,] FALSE     FALSE       FALSE        FALSE
##  [231,]  TRUE      TRUE       FALSE        FALSE
##  [232,] FALSE      TRUE       FALSE        FALSE
##  [233,] FALSE      TRUE       FALSE         TRUE
##  [234,]  TRUE      TRUE       FALSE         TRUE
##  [235,] FALSE      TRUE       FALSE        FALSE
##  [236,]  TRUE     FALSE       FALSE         TRUE
##  [237,] FALSE     FALSE       FALSE        FALSE
##  [238,] FALSE      TRUE       FALSE         TRUE
##  [239,]  TRUE      TRUE       FALSE         TRUE
##  [240,] FALSE     FALSE       FALSE        FALSE
##  [241,] FALSE     FALSE       FALSE        FALSE
##  [242,] FALSE     FALSE       FALSE         TRUE
##  [243,] FALSE     FALSE       FALSE        FALSE
##  [244,]  TRUE     FALSE       FALSE         TRUE
##  [245,]  TRUE     FALSE       FALSE        FALSE
##  [246,] FALSE      TRUE        TRUE         TRUE
##  [247,] FALSE     FALSE       FALSE         TRUE
##  [248,]  TRUE      TRUE       FALSE         TRUE
##  [249,]  TRUE      TRUE       FALSE         TRUE
##  [250,]  TRUE      TRUE       FALSE         TRUE
##  [251,] FALSE      TRUE       FALSE        FALSE
##  [252,]  TRUE     FALSE       FALSE         TRUE
##  [253,] FALSE     FALSE       FALSE        FALSE
##  [254,]  TRUE     FALSE       FALSE         TRUE
##  [255,] FALSE     FALSE       FALSE        FALSE
##  [256,]  TRUE      TRUE       FALSE         TRUE
##  [257,] FALSE     FALSE       FALSE        FALSE
##  [258,]  TRUE      TRUE       FALSE         TRUE
##  [259,]  TRUE      TRUE       FALSE         TRUE
##  [260,] FALSE     FALSE       FALSE        FALSE
##  [261,]  TRUE      TRUE       FALSE         TRUE
##  [262,] FALSE      TRUE       FALSE        FALSE
##  [263,]  TRUE     FALSE       FALSE         TRUE
##  [264,]  TRUE     FALSE       FALSE         TRUE
##  [265,] FALSE     FALSE       FALSE         TRUE
##  [266,] FALSE     FALSE       FALSE         TRUE
##  [267,] FALSE     FALSE       FALSE        FALSE
##  [268,] FALSE     FALSE       FALSE        FALSE
##  [269,] FALSE     FALSE       FALSE        FALSE
##  [270,] FALSE     FALSE       FALSE        FALSE
##  [271,]  TRUE     FALSE       FALSE        FALSE
##  [272,]  TRUE      TRUE       FALSE         TRUE
##  [273,]  TRUE      TRUE       FALSE         TRUE
##  [274,] FALSE     FALSE       FALSE        FALSE
##  [275,] FALSE     FALSE       FALSE         TRUE
##  [276,] FALSE     FALSE       FALSE         TRUE
##  [277,]  TRUE      TRUE       FALSE         TRUE
##  [278,]  TRUE     FALSE       FALSE         TRUE
##  [279,] FALSE     FALSE       FALSE        FALSE
##  [280,] FALSE      TRUE       FALSE        FALSE
##  [281,] FALSE     FALSE       FALSE        FALSE
##  [282,] FALSE      TRUE       FALSE        FALSE
##  [283,] FALSE     FALSE       FALSE        FALSE
##  [284,] FALSE     FALSE       FALSE        FALSE
##  [285,] FALSE      TRUE       FALSE        FALSE
##  [286,] FALSE     FALSE       FALSE        FALSE
##  [287,]  TRUE      TRUE       FALSE        FALSE
##  [288,] FALSE     FALSE       FALSE        FALSE
##  [289,] FALSE     FALSE       FALSE        FALSE
##  [290,] FALSE     FALSE       FALSE         TRUE
##  [291,] FALSE     FALSE       FALSE        FALSE
##  [292,] FALSE     FALSE       FALSE        FALSE
##  [293,]  TRUE      TRUE       FALSE        FALSE
##  [294,] FALSE      TRUE       FALSE        FALSE
##  [295,] FALSE     FALSE       FALSE        FALSE
##  [296,] FALSE     FALSE       FALSE        FALSE
##  [297,] FALSE     FALSE       FALSE        FALSE
##  [298,] FALSE     FALSE       FALSE        FALSE
##  [299,] FALSE     FALSE       FALSE        FALSE
##  [300,] FALSE     FALSE       FALSE        FALSE
##  [301,]  TRUE     FALSE       FALSE        FALSE
##  [302,] FALSE     FALSE       FALSE        FALSE
##  [303,] FALSE     FALSE       FALSE        FALSE
##  [304,] FALSE     FALSE       FALSE        FALSE
##  [305,] FALSE     FALSE       FALSE        FALSE
##  [306,] FALSE     FALSE       FALSE        FALSE
##  [307,] FALSE     FALSE       FALSE         TRUE
##  [308,] FALSE     FALSE       FALSE         TRUE
##  [309,] FALSE     FALSE       FALSE        FALSE
##  [310,] FALSE     FALSE       FALSE        FALSE
##  [311,] FALSE     FALSE       FALSE        FALSE
##  [312,] FALSE     FALSE       FALSE         TRUE
##  [313,] FALSE     FALSE       FALSE        FALSE
##  [314,] FALSE     FALSE       FALSE        FALSE
##  [315,] FALSE     FALSE       FALSE         TRUE
##  [316,] FALSE     FALSE       FALSE         TRUE
##  [317,] FALSE     FALSE       FALSE        FALSE
##  [318,] FALSE      TRUE       FALSE        FALSE
##  [319,] FALSE     FALSE       FALSE        FALSE
##  [320,] FALSE     FALSE       FALSE        FALSE
##  [321,] FALSE     FALSE       FALSE        FALSE
##  [322,] FALSE      TRUE       FALSE         TRUE
##  [323,]  TRUE     FALSE       FALSE         TRUE
##  [324,] FALSE     FALSE       FALSE        FALSE
##  [325,] FALSE      TRUE       FALSE         TRUE
##  [326,] FALSE     FALSE       FALSE         TRUE
##  [327,] FALSE     FALSE       FALSE        FALSE
##  [328,]  TRUE      TRUE       FALSE         TRUE
##  [329,] FALSE     FALSE       FALSE        FALSE
##  [330,] FALSE     FALSE       FALSE        FALSE
##  [331,] FALSE     FALSE       FALSE        FALSE
##  [332,] FALSE     FALSE       FALSE         TRUE
##  [333,]  TRUE     FALSE       FALSE         TRUE
##  [334,]  TRUE     FALSE       FALSE         TRUE
##  [335,] FALSE     FALSE       FALSE         TRUE
##  [336,] FALSE     FALSE       FALSE        FALSE
##  [337,] FALSE     FALSE       FALSE        FALSE
##  [338,] FALSE     FALSE       FALSE        FALSE
##  [339,] FALSE     FALSE       FALSE        FALSE
##  [340,] FALSE     FALSE       FALSE        FALSE
##  [341,]  TRUE      TRUE       FALSE        FALSE
##  [342,] FALSE     FALSE       FALSE        FALSE
##  [343,] FALSE     FALSE       FALSE         TRUE
##  [344,]  TRUE      TRUE       FALSE         TRUE
##  [345,]  TRUE      TRUE       FALSE         TRUE
##  [346,] FALSE     FALSE       FALSE         TRUE
##  [347,]  TRUE      TRUE       FALSE         TRUE
##  [348,] FALSE     FALSE       FALSE        FALSE
##  [349,]  TRUE      TRUE       FALSE        FALSE
##  [350,]  TRUE     FALSE       FALSE         TRUE
##  [351,] FALSE     FALSE       FALSE        FALSE
##  [352,]  TRUE     FALSE       FALSE        FALSE
##  [353,]  TRUE      TRUE       FALSE         TRUE
##  [354,] FALSE     FALSE       FALSE        FALSE
##  [355,]  TRUE      TRUE       FALSE         TRUE
##  [356,]  TRUE     FALSE       FALSE         TRUE
##  [357,]  TRUE     FALSE       FALSE        FALSE
##  [358,]  TRUE     FALSE       FALSE         TRUE
##  [359,] FALSE     FALSE       FALSE        FALSE
##  [360,] FALSE      TRUE       FALSE         TRUE
##  [361,] FALSE     FALSE       FALSE        FALSE
##  [362,] FALSE     FALSE       FALSE        FALSE
##  [363,] FALSE     FALSE       FALSE        FALSE
##  [364,] FALSE     FALSE       FALSE        FALSE
##  [365,] FALSE     FALSE       FALSE        FALSE
##  [366,] FALSE     FALSE       FALSE        FALSE
##  [367,] FALSE     FALSE       FALSE        FALSE
##  [368,]  TRUE      TRUE        TRUE         TRUE
##  [369,] FALSE     FALSE       FALSE         TRUE
##  [370,] FALSE     FALSE       FALSE        FALSE
##  [371,] FALSE     FALSE       FALSE        FALSE
##  [372,]  TRUE      TRUE       FALSE         TRUE
##  [373,] FALSE     FALSE       FALSE        FALSE
##  [374,] FALSE     FALSE       FALSE        FALSE
##  [375,]  TRUE      TRUE        TRUE         TRUE
##  [376,] FALSE     FALSE       FALSE         TRUE
##  [377,]  TRUE      TRUE       FALSE         TRUE
##  [378,] FALSE     FALSE       FALSE         TRUE
##  [379,] FALSE     FALSE       FALSE        FALSE
##  [380,]  TRUE      TRUE       FALSE        FALSE
##  [381,]  TRUE     FALSE       FALSE         TRUE
##  [382,] FALSE     FALSE       FALSE        FALSE
##  [383,] FALSE     FALSE        TRUE         TRUE
##  [384,] FALSE     FALSE        TRUE        FALSE
##  [385,]  TRUE      TRUE       FALSE         TRUE
##  [386,] FALSE     FALSE       FALSE        FALSE
##  [387,] FALSE      TRUE       FALSE        FALSE
##  [388,] FALSE     FALSE       FALSE        FALSE
##  [389,] FALSE     FALSE        TRUE         TRUE
##  [390,] FALSE     FALSE       FALSE        FALSE
##  [391,] FALSE     FALSE       FALSE        FALSE
##  [392,] FALSE     FALSE       FALSE        FALSE
##  [393,] FALSE     FALSE       FALSE        FALSE
##  [394,]  TRUE      TRUE       FALSE         TRUE
##  [395,] FALSE     FALSE       FALSE        FALSE
##  [396,]  TRUE      TRUE       FALSE         TRUE
##  [397,] FALSE     FALSE       FALSE         TRUE
##  [398,] FALSE     FALSE       FALSE        FALSE
##  [399,]  TRUE      TRUE       FALSE         TRUE
##  [400,]  TRUE      TRUE       FALSE        FALSE
##  [401,] FALSE     FALSE       FALSE        FALSE
##  [402,]  TRUE     FALSE       FALSE         TRUE
##  [403,] FALSE     FALSE       FALSE        FALSE
##  [404,]  TRUE      TRUE       FALSE         TRUE
##  [405,] FALSE     FALSE       FALSE         TRUE
##  [406,] FALSE     FALSE       FALSE        FALSE
##  [407,]  TRUE      TRUE        TRUE         TRUE
##  [408,] FALSE     FALSE        TRUE         TRUE
##  [409,] FALSE     FALSE       FALSE        FALSE
##  [410,]  TRUE      TRUE        TRUE         TRUE
##  [411,] FALSE     FALSE        TRUE         TRUE
##  [412,]  TRUE      TRUE       FALSE         TRUE
##  [413,]  TRUE     FALSE       FALSE         TRUE
##  [414,]  TRUE      TRUE       FALSE         TRUE
##  [415,]  TRUE     FALSE       FALSE         TRUE
##  [416,]  TRUE     FALSE       FALSE         TRUE
##  [417,] FALSE      TRUE       FALSE         TRUE
##  [418,] FALSE      TRUE       FALSE         TRUE
##  [419,] FALSE     FALSE       FALSE         TRUE
##  [420,]  TRUE      TRUE       FALSE         TRUE
##  [421,]  TRUE      TRUE        TRUE         TRUE
##  [422,]  TRUE      TRUE       FALSE         TRUE
##  [423,]  TRUE      TRUE       FALSE         TRUE
##  [424,]  TRUE      TRUE       FALSE         TRUE
##  [425,]  TRUE      TRUE       FALSE         TRUE
##  [426,] FALSE      TRUE       FALSE        FALSE
##  [427,] FALSE      TRUE       FALSE        FALSE
##  [428,]  TRUE     FALSE       FALSE        FALSE
##  [429,] FALSE     FALSE       FALSE         TRUE
##  [430,] FALSE     FALSE       FALSE        FALSE
##  [431,] FALSE      TRUE       FALSE        FALSE
##  [432,]  TRUE     FALSE       FALSE        FALSE
##  [433,]  TRUE      TRUE       FALSE        FALSE
##  [434,] FALSE      TRUE       FALSE        FALSE
##  [435,]  TRUE      TRUE       FALSE        FALSE
##  [436,]  TRUE     FALSE       FALSE         TRUE
##  [437,]  TRUE     FALSE       FALSE        FALSE
##  [438,]  TRUE      TRUE       FALSE         TRUE
##  [439,]  TRUE     FALSE       FALSE        FALSE
##  [440,] FALSE     FALSE       FALSE        FALSE
##  [441,]  TRUE      TRUE       FALSE         TRUE
##  [442,]  TRUE      TRUE       FALSE         TRUE
##  [443,]  TRUE      TRUE       FALSE         TRUE
##  [444,]  TRUE      TRUE       FALSE         TRUE
##  [445,]  TRUE      TRUE       FALSE         TRUE
##  [446,]  TRUE      TRUE       FALSE         TRUE
##  [447,]  TRUE      TRUE       FALSE         TRUE
##  [448,]  TRUE      TRUE       FALSE         TRUE
##  [449,]  TRUE      TRUE       FALSE         TRUE
##  [450,] FALSE     FALSE       FALSE         TRUE
##  [451,]  TRUE      TRUE       FALSE        FALSE
##  [452,] FALSE     FALSE       FALSE        FALSE
##  [453,] FALSE     FALSE       FALSE        FALSE
##  [454,] FALSE     FALSE       FALSE         TRUE
##  [455,]  TRUE      TRUE       FALSE         TRUE
##  [456,]  TRUE     FALSE       FALSE         TRUE
##  [457,]  TRUE      TRUE       FALSE         TRUE
##  [458,]  TRUE      TRUE       FALSE         TRUE
##  [459,] FALSE     FALSE       FALSE         TRUE
##  [460,] FALSE     FALSE       FALSE        FALSE
##  [461,]  TRUE      TRUE       FALSE        FALSE
##  [462,] FALSE      TRUE        TRUE         TRUE
##  [463,]  TRUE      TRUE       FALSE         TRUE
##  [464,]  TRUE      TRUE       FALSE         TRUE
##  [465,]  TRUE      TRUE       FALSE         TRUE
##  [466,]  TRUE      TRUE       FALSE         TRUE
##  [467,]  TRUE     FALSE       FALSE         TRUE
##  [468,]  TRUE      TRUE       FALSE         TRUE
##  [469,] FALSE     FALSE       FALSE         TRUE
##  [470,] FALSE     FALSE       FALSE         TRUE
##  [471,] FALSE     FALSE       FALSE        FALSE
##  [472,]  TRUE      TRUE       FALSE        FALSE
##  [473,] FALSE     FALSE       FALSE        FALSE
##  [474,]  TRUE     FALSE       FALSE        FALSE
##  [475,] FALSE     FALSE       FALSE        FALSE
##  [476,]  TRUE      TRUE       FALSE        FALSE
##  [477,] FALSE     FALSE       FALSE        FALSE
##  [478,]  TRUE     FALSE       FALSE        FALSE
##  [479,]  TRUE      TRUE       FALSE        FALSE
##  [480,] FALSE     FALSE       FALSE        FALSE
##  [481,]  TRUE     FALSE       FALSE        FALSE
##  [482,] FALSE     FALSE       FALSE        FALSE
##  [483,] FALSE     FALSE       FALSE         TRUE
##  [484,]  TRUE      TRUE       FALSE         TRUE
##  [485,] FALSE     FALSE       FALSE         TRUE
##  [486,]  TRUE      TRUE       FALSE         TRUE
##  [487,]  TRUE      TRUE       FALSE         TRUE
##  [488,]  TRUE      TRUE       FALSE         TRUE
##  [489,] FALSE      TRUE       FALSE        FALSE
##  [490,] FALSE      TRUE       FALSE        FALSE
##  [491,] FALSE     FALSE       FALSE        FALSE
##  [492,]  TRUE      TRUE       FALSE         TRUE
##  [493,] FALSE     FALSE       FALSE        FALSE
##  [494,] FALSE     FALSE       FALSE        FALSE
##  [495,] FALSE      TRUE       FALSE        FALSE
##  [496,] FALSE      TRUE       FALSE        FALSE
##  [497,]  TRUE      TRUE       FALSE        FALSE
##  [498,]  TRUE      TRUE       FALSE        FALSE
##  [499,] FALSE     FALSE       FALSE        FALSE
##  [500,] FALSE      TRUE       FALSE        FALSE
##  [501,] FALSE     FALSE       FALSE        FALSE
##  [502,]  TRUE     FALSE       FALSE        FALSE
##  [503,] FALSE     FALSE       FALSE        FALSE
##  [504,]  TRUE      TRUE       FALSE        FALSE
##  [505,] FALSE     FALSE       FALSE         TRUE
##  [506,] FALSE     FALSE       FALSE         TRUE
##  [507,]  TRUE      TRUE       FALSE         TRUE
##  [508,]  TRUE      TRUE       FALSE        FALSE
##  [509,] FALSE     FALSE       FALSE        FALSE
##  [510,] FALSE     FALSE       FALSE        FALSE
##  [511,] FALSE      TRUE       FALSE        FALSE
##  [512,]  TRUE      TRUE       FALSE         TRUE
##  [513,]  TRUE      TRUE        TRUE        FALSE
##  [514,] FALSE      TRUE       FALSE         TRUE
##  [515,]  TRUE      TRUE       FALSE         TRUE
##  [516,]  TRUE      TRUE       FALSE         TRUE
##  [517,] FALSE      TRUE       FALSE         TRUE
##  [518,] FALSE      TRUE       FALSE        FALSE
##  [519,] FALSE      TRUE       FALSE        FALSE
##  [520,] FALSE     FALSE       FALSE        FALSE
##  [521,] FALSE      TRUE       FALSE        FALSE
##  [522,] FALSE     FALSE       FALSE        FALSE
##  [523,] FALSE      TRUE       FALSE        FALSE
##  [524,] FALSE      TRUE       FALSE        FALSE
##  [525,] FALSE     FALSE       FALSE        FALSE
##  [526,] FALSE     FALSE       FALSE        FALSE
##  [527,]  TRUE      TRUE       FALSE        FALSE
##  [528,] FALSE     FALSE       FALSE        FALSE
##  [529,]  TRUE      TRUE       FALSE         TRUE
##  [530,]  TRUE      TRUE       FALSE         TRUE
##  [531,]  TRUE      TRUE       FALSE         TRUE
##  [532,]  TRUE      TRUE       FALSE         TRUE
##  [533,] FALSE      TRUE       FALSE         TRUE
##  [534,] FALSE      TRUE       FALSE        FALSE
##  [535,] FALSE      TRUE       FALSE         TRUE
##  [536,] FALSE      TRUE       FALSE        FALSE
##  [537,] FALSE      TRUE       FALSE        FALSE
##  [538,] FALSE      TRUE       FALSE        FALSE
##  [539,] FALSE      TRUE       FALSE        FALSE
##  [540,] FALSE     FALSE       FALSE        FALSE
##  [541,] FALSE      TRUE       FALSE        FALSE
##  [542,] FALSE      TRUE       FALSE         TRUE
##  [543,]  TRUE      TRUE       FALSE         TRUE
##  [544,] FALSE     FALSE       FALSE        FALSE
##  [545,] FALSE      TRUE       FALSE        FALSE
##  [546,] FALSE      TRUE       FALSE        FALSE
##  [547,] FALSE      TRUE       FALSE        FALSE
##  [548,] FALSE     FALSE       FALSE        FALSE
##  [549,]  TRUE      TRUE        TRUE        FALSE
##  [550,] FALSE      TRUE       FALSE        FALSE
##  [551,] FALSE      TRUE       FALSE        FALSE
##  [552,] FALSE      TRUE       FALSE        FALSE
##  [553,]  TRUE      TRUE        TRUE         TRUE
##  [554,]  TRUE      TRUE       FALSE         TRUE
##  [555,] FALSE     FALSE       FALSE         TRUE
##  [556,] FALSE      TRUE       FALSE         TRUE
##  [557,]  TRUE     FALSE       FALSE        FALSE
##  [558,] FALSE     FALSE       FALSE        FALSE
##  [559,]  TRUE      TRUE       FALSE        FALSE
##  [560,] FALSE      TRUE       FALSE         TRUE
##  [561,]  TRUE      TRUE       FALSE         TRUE
##  [562,] FALSE     FALSE       FALSE        FALSE
##  [563,] FALSE     FALSE       FALSE         TRUE
##  [564,]  TRUE     FALSE        TRUE        FALSE
##  [565,] FALSE     FALSE       FALSE        FALSE
##  [566,] FALSE     FALSE       FALSE        FALSE
##  [567,] FALSE     FALSE       FALSE        FALSE
##  [568,] FALSE     FALSE       FALSE        FALSE
##  [569,] FALSE     FALSE       FALSE        FALSE
##  [570,] FALSE     FALSE       FALSE        FALSE
##  [571,] FALSE     FALSE       FALSE        FALSE
##  [572,] FALSE     FALSE       FALSE        FALSE
##  [573,] FALSE     FALSE       FALSE        FALSE
##  [574,]  TRUE      TRUE       FALSE         TRUE
##  [575,] FALSE     FALSE       FALSE         TRUE
##  [576,] FALSE      TRUE       FALSE        FALSE
##  [577,]  TRUE      TRUE       FALSE        FALSE
##  [578,]  TRUE      TRUE        TRUE         TRUE
##  [579,]  TRUE      TRUE       FALSE         TRUE
##  [580,]  TRUE      TRUE       FALSE         TRUE
##  [581,] FALSE      TRUE       FALSE         TRUE
##  [582,]  TRUE     FALSE       FALSE         TRUE
##  [583,]  TRUE      TRUE       FALSE         TRUE
##  [584,]  TRUE      TRUE       FALSE        FALSE
##  [585,] FALSE     FALSE       FALSE        FALSE
##  [586,]  TRUE      TRUE        TRUE         TRUE
##  [587,]  TRUE      TRUE       FALSE         TRUE
##  [588,] FALSE     FALSE       FALSE         TRUE
##  [589,]  TRUE      TRUE       FALSE         TRUE
##  [590,]  TRUE      TRUE       FALSE         TRUE
##  [591,] FALSE     FALSE       FALSE        FALSE
##  [592,] FALSE     FALSE       FALSE        FALSE
##  [593,]  TRUE      TRUE        TRUE         TRUE
##  [594,]  TRUE     FALSE        TRUE         TRUE
##  [595,]  TRUE      TRUE       FALSE         TRUE
##  [596,] FALSE      TRUE       FALSE         TRUE
##  [597,]  TRUE      TRUE       FALSE         TRUE
##  [598,] FALSE      TRUE       FALSE         TRUE
##  [599,] FALSE     FALSE       FALSE        FALSE
##  [600,] FALSE     FALSE       FALSE        FALSE
##  [601,]  TRUE      TRUE       FALSE        FALSE
##  [602,] FALSE     FALSE       FALSE        FALSE
##  [603,]  TRUE      TRUE       FALSE         TRUE
##  [604,] FALSE      TRUE       FALSE         TRUE
##  [605,] FALSE     FALSE       FALSE        FALSE
##  [606,] FALSE      TRUE       FALSE        FALSE
##  [607,] FALSE     FALSE       FALSE         TRUE
##  [608,]  TRUE      TRUE       FALSE        FALSE
##  [609,]  TRUE      TRUE       FALSE        FALSE
##  [610,]  TRUE      TRUE       FALSE         TRUE
##  [611,] FALSE     FALSE       FALSE        FALSE
##  [612,] FALSE      TRUE       FALSE        FALSE
##  [613,] FALSE     FALSE       FALSE        FALSE
##  [614,] FALSE      TRUE       FALSE        FALSE
##  [615,] FALSE     FALSE       FALSE        FALSE
##  [616,] FALSE      TRUE       FALSE         TRUE
##  [617,]  TRUE      TRUE       FALSE        FALSE
##  [618,] FALSE     FALSE       FALSE        FALSE
##  [619,] FALSE      TRUE       FALSE         TRUE
##  [620,] FALSE     FALSE       FALSE        FALSE
##  [621,] FALSE     FALSE       FALSE        FALSE
##  [622,] FALSE     FALSE       FALSE        FALSE
##  [623,] FALSE      TRUE       FALSE         TRUE
##  [624,] FALSE     FALSE       FALSE         TRUE
##  [625,] FALSE     FALSE       FALSE        FALSE
##  [626,]  TRUE     FALSE       FALSE         TRUE
##  [627,] FALSE     FALSE       FALSE         TRUE
##  [628,]  TRUE      TRUE       FALSE         TRUE
##  [629,] FALSE     FALSE       FALSE        FALSE
##  [630,]  TRUE     FALSE       FALSE         TRUE
##  [631,] FALSE     FALSE       FALSE         TRUE
##  [632,] FALSE     FALSE       FALSE         TRUE
##  [633,]  TRUE      TRUE        TRUE         TRUE
##  [634,]  TRUE      TRUE       FALSE         TRUE
##  [635,]  TRUE      TRUE       FALSE         TRUE
##  [636,]  TRUE      TRUE       FALSE         TRUE
##  [637,]  TRUE      TRUE       FALSE         TRUE
##  [638,] FALSE      TRUE       FALSE         TRUE
##  [639,] FALSE      TRUE       FALSE         TRUE
##  [640,] FALSE      TRUE       FALSE         TRUE
##  [641,]  TRUE     FALSE       FALSE         TRUE
##  [642,] FALSE      TRUE       FALSE        FALSE
##  [643,] FALSE      TRUE       FALSE        FALSE
##  [644,] FALSE      TRUE       FALSE        FALSE
##  [645,] FALSE     FALSE       FALSE        FALSE
##  [646,] FALSE     FALSE       FALSE        FALSE
##  [647,]  TRUE      TRUE       FALSE         TRUE
##  [648,]  TRUE      TRUE       FALSE         TRUE
##  [649,] FALSE     FALSE       FALSE        FALSE
##  [650,] FALSE     FALSE       FALSE        FALSE
##  [651,] FALSE     FALSE       FALSE        FALSE
##  [652,] FALSE     FALSE       FALSE        FALSE
##  [653,]  TRUE      TRUE       FALSE        FALSE
##  [654,] FALSE      TRUE       FALSE        FALSE
##  [655,] FALSE      TRUE       FALSE        FALSE
##  [656,] FALSE      TRUE       FALSE        FALSE
##  [657,] FALSE      TRUE       FALSE        FALSE
##  [658,] FALSE     FALSE       FALSE        FALSE
##  [659,] FALSE     FALSE       FALSE        FALSE
##  [660,] FALSE      TRUE       FALSE        FALSE
##  [661,] FALSE     FALSE       FALSE        FALSE
##  [662,]  TRUE      TRUE       FALSE        FALSE
##  [663,] FALSE     FALSE       FALSE        FALSE
##  [664,] FALSE     FALSE       FALSE        FALSE
##  [665,] FALSE      TRUE       FALSE        FALSE
##  [666,] FALSE     FALSE       FALSE         TRUE
##  [667,] FALSE     FALSE       FALSE        FALSE
##  [668,] FALSE     FALSE       FALSE        FALSE
##  [669,] FALSE     FALSE       FALSE        FALSE
##  [670,] FALSE     FALSE       FALSE        FALSE
##  [671,]  TRUE      TRUE       FALSE         TRUE
##  [672,]  TRUE      TRUE       FALSE         TRUE
##  [673,]  TRUE      TRUE       FALSE         TRUE
##  [674,]  TRUE      TRUE       FALSE         TRUE
##  [675,]  TRUE      TRUE       FALSE         TRUE
##  [676,] FALSE      TRUE       FALSE         TRUE
##  [677,]  TRUE      TRUE        TRUE         TRUE
##  [678,]  TRUE      TRUE       FALSE         TRUE
##  [679,] FALSE     FALSE       FALSE        FALSE
##  [680,] FALSE     FALSE       FALSE        FALSE
##  [681,] FALSE      TRUE        TRUE         TRUE
##  [682,]  TRUE      TRUE       FALSE         TRUE
##  [683,]  TRUE      TRUE       FALSE         TRUE
##  [684,] FALSE      TRUE       FALSE         TRUE
##  [685,] FALSE      TRUE       FALSE        FALSE
##  [686,] FALSE      TRUE       FALSE        FALSE
##  [687,]  TRUE      TRUE       FALSE        FALSE
##  [688,]  TRUE     FALSE       FALSE         TRUE
##  [689,] FALSE     FALSE       FALSE        FALSE
##  [690,]  TRUE      TRUE       FALSE         TRUE
##  [691,] FALSE     FALSE       FALSE        FALSE
##  [692,] FALSE     FALSE       FALSE        FALSE
##  [693,]  TRUE     FALSE       FALSE         TRUE
##  [694,] FALSE     FALSE       FALSE        FALSE
##  [695,]  TRUE      TRUE        TRUE         TRUE
##  [696,] FALSE      TRUE       FALSE         TRUE
##  [697,] FALSE      TRUE       FALSE        FALSE
##  [698,] FALSE     FALSE       FALSE        FALSE
##  [699,] FALSE     FALSE       FALSE        FALSE
##  [700,] FALSE     FALSE       FALSE        FALSE
##  [701,] FALSE     FALSE       FALSE        FALSE
##  [702,] FALSE     FALSE       FALSE        FALSE
##  [703,] FALSE     FALSE       FALSE        FALSE
##  [704,] FALSE     FALSE       FALSE        FALSE
##  [705,] FALSE      TRUE       FALSE        FALSE
##  [706,] FALSE     FALSE       FALSE        FALSE
##  [707,]  TRUE      TRUE       FALSE         TRUE
##  [708,]  TRUE      TRUE       FALSE         TRUE
##  [709,] FALSE      TRUE       FALSE         TRUE
##  [710,] FALSE      TRUE       FALSE         TRUE
##  [711,]  TRUE     FALSE       FALSE         TRUE
##  [712,] FALSE     FALSE       FALSE        FALSE
##  [713,] FALSE     FALSE       FALSE        FALSE
##  [714,] FALSE      TRUE       FALSE        FALSE
##  [715,] FALSE      TRUE       FALSE        FALSE
##  [716,] FALSE      TRUE        TRUE         TRUE
##  [717,] FALSE      TRUE        TRUE        FALSE
##  [718,] FALSE     FALSE       FALSE        FALSE
##  [719,]  TRUE      TRUE       FALSE         TRUE
##  [720,] FALSE     FALSE       FALSE        FALSE
##  [721,] FALSE      TRUE       FALSE        FALSE
##  [722,] FALSE      TRUE       FALSE        FALSE
##  [723,] FALSE     FALSE       FALSE        FALSE
##  [724,]  TRUE      TRUE        TRUE        FALSE
##  [725,] FALSE     FALSE       FALSE        FALSE
##  [726,] FALSE     FALSE       FALSE        FALSE
##  [727,] FALSE     FALSE       FALSE        FALSE
##  [728,] FALSE     FALSE       FALSE        FALSE
##  [729,] FALSE     FALSE       FALSE        FALSE
##  [730,]  TRUE      TRUE       FALSE         TRUE
##  [731,] FALSE     FALSE       FALSE        FALSE
##  [732,] FALSE     FALSE       FALSE        FALSE
##  [733,] FALSE      TRUE        TRUE         TRUE
##  [734,] FALSE      TRUE        TRUE         TRUE
##  [735,] FALSE     FALSE       FALSE        FALSE
##  [736,]  TRUE      TRUE       FALSE         TRUE
##  [737,]  TRUE      TRUE       FALSE         TRUE
##  [738,]  TRUE     FALSE       FALSE         TRUE
##  [739,] FALSE      TRUE       FALSE        FALSE
##  [740,] FALSE     FALSE       FALSE        FALSE
##  [741,] FALSE     FALSE       FALSE        FALSE
##  [742,] FALSE     FALSE       FALSE        FALSE
##  [743,]  TRUE      TRUE       FALSE         TRUE
##  [744,]  TRUE     FALSE       FALSE        FALSE
##  [745,] FALSE     FALSE       FALSE        FALSE
##  [746,]  TRUE      TRUE       FALSE         TRUE
##  [747,]  TRUE      TRUE       FALSE        FALSE
##  [748,]  TRUE      TRUE       FALSE        FALSE
##  [749,]  TRUE      TRUE       FALSE         TRUE
##  [750,] FALSE     FALSE       FALSE        FALSE
##  [751,]  TRUE      TRUE       FALSE        FALSE
##  [752,]  TRUE      TRUE       FALSE        FALSE
##  [753,] FALSE      TRUE       FALSE        FALSE
##  [754,] FALSE     FALSE       FALSE        FALSE
##  [755,]  TRUE      TRUE        TRUE        FALSE
##  [756,] FALSE     FALSE       FALSE        FALSE
##  [757,]  TRUE      TRUE       FALSE        FALSE
##  [758,] FALSE     FALSE       FALSE        FALSE
##  [759,] FALSE     FALSE       FALSE         TRUE
##  [760,]  TRUE      TRUE       FALSE         TRUE
##  [761,] FALSE     FALSE       FALSE        FALSE
##  [762,]  TRUE      TRUE       FALSE         TRUE
##  [763,]  TRUE      TRUE       FALSE         TRUE
##  [764,]  TRUE     FALSE       FALSE         TRUE
##  [765,] FALSE     FALSE       FALSE         TRUE
##  [766,] FALSE      TRUE       FALSE        FALSE
##  [767,] FALSE      TRUE       FALSE         TRUE
##  [768,] FALSE      TRUE       FALSE         TRUE
##  [769,] FALSE      TRUE       FALSE         TRUE
##  [770,]  TRUE      TRUE       FALSE         TRUE
##  [771,] FALSE     FALSE       FALSE        FALSE
##  [772,]  TRUE     FALSE       FALSE         TRUE
##  [773,] FALSE     FALSE       FALSE         TRUE
##  [774,] FALSE     FALSE       FALSE         TRUE
##  [775,] FALSE      TRUE       FALSE        FALSE
##  [776,] FALSE     FALSE       FALSE        FALSE
##  [777,] FALSE      TRUE       FALSE        FALSE
##  [778,]  TRUE      TRUE       FALSE         TRUE
##  [779,] FALSE     FALSE       FALSE        FALSE
##  [780,] FALSE     FALSE       FALSE        FALSE
##  [781,] FALSE      TRUE       FALSE         TRUE
##  [782,] FALSE      TRUE       FALSE        FALSE
##  [783,]  TRUE      TRUE       FALSE         TRUE
##  [784,]  TRUE      TRUE       FALSE         TRUE
##  [785,]  TRUE      TRUE       FALSE         TRUE
##  [786,]  TRUE      TRUE       FALSE         TRUE
##  [787,] FALSE      TRUE       FALSE         TRUE
##  [788,]  TRUE      TRUE       FALSE        FALSE
##  [789,] FALSE      TRUE       FALSE         TRUE
##  [790,]  TRUE      TRUE       FALSE         TRUE
##  [791,] FALSE     FALSE       FALSE         TRUE
##  [792,] FALSE      TRUE       FALSE         TRUE
##  [793,]  TRUE     FALSE       FALSE         TRUE
##  [794,]  TRUE      TRUE        TRUE         TRUE
##  [795,]  TRUE     FALSE       FALSE         TRUE
##  [796,]  TRUE      TRUE        TRUE        FALSE
##  [797,] FALSE      TRUE       FALSE        FALSE
##  [798,] FALSE      TRUE       FALSE        FALSE
##  [799,]  TRUE      TRUE        TRUE        FALSE
##  [800,]  TRUE      TRUE       FALSE         TRUE
##  [801,] FALSE     FALSE       FALSE        FALSE
##  [802,]  TRUE      TRUE       FALSE        FALSE
##  [803,] FALSE     FALSE       FALSE        FALSE
##  [804,]  TRUE      TRUE       FALSE         TRUE
##  [805,] FALSE      TRUE       FALSE        FALSE
##  [806,]  TRUE      TRUE       FALSE        FALSE
##  [807,]  TRUE      TRUE       FALSE        FALSE
##  [808,] FALSE     FALSE       FALSE        FALSE
##  [809,]  TRUE      TRUE       FALSE        FALSE
##  [810,]  TRUE     FALSE       FALSE        FALSE
##  [811,] FALSE      TRUE       FALSE        FALSE
##  [812,] FALSE     FALSE       FALSE        FALSE
##  [813,]  TRUE      TRUE       FALSE         TRUE
##  [814,]  TRUE      TRUE       FALSE         TRUE
##  [815,]  TRUE      TRUE       FALSE         TRUE
##  [816,]  TRUE     FALSE       FALSE         TRUE
##  [817,] FALSE     FALSE       FALSE         TRUE
##  [818,] FALSE      TRUE       FALSE        FALSE
##  [819,] FALSE     FALSE       FALSE        FALSE
##  [820,] FALSE      TRUE       FALSE        FALSE
##  [821,] FALSE     FALSE       FALSE         TRUE
##  [822,] FALSE     FALSE       FALSE        FALSE
##  [823,]  TRUE      TRUE       FALSE         TRUE
##  [824,]  TRUE      TRUE       FALSE         TRUE
##  [825,] FALSE     FALSE       FALSE        FALSE
##  [826,] FALSE      TRUE       FALSE        FALSE
##  [827,]  TRUE     FALSE       FALSE        FALSE
##  [828,]  TRUE      TRUE       FALSE        FALSE
##  [829,] FALSE      TRUE       FALSE         TRUE
##  [830,]  TRUE      TRUE       FALSE        FALSE
##  [831,] FALSE      TRUE       FALSE         TRUE
##  [832,]  TRUE      TRUE       FALSE         TRUE
##  [833,]  TRUE      TRUE       FALSE        FALSE
##  [834,] FALSE      TRUE       FALSE        FALSE
##  [835,] FALSE     FALSE       FALSE        FALSE
##  [836,]  TRUE      TRUE       FALSE        FALSE
##  [837,]  TRUE      TRUE       FALSE        FALSE
##  [838,]  TRUE      TRUE       FALSE        FALSE
##  [839,]  TRUE     FALSE       FALSE         TRUE
##  [840,]  TRUE      TRUE       FALSE         TRUE
##  [841,] FALSE      TRUE       FALSE         TRUE
##  [842,]  TRUE      TRUE       FALSE         TRUE
##  [843,] FALSE     FALSE        TRUE        FALSE
##  [844,] FALSE      TRUE       FALSE        FALSE
##  [845,]  TRUE      TRUE       FALSE        FALSE
##  [846,]  TRUE      TRUE       FALSE         TRUE
##  [847,] FALSE      TRUE       FALSE         TRUE
##  [848,] FALSE      TRUE       FALSE         TRUE
##  [849,]  TRUE      TRUE       FALSE        FALSE
##  [850,]  TRUE      TRUE       FALSE        FALSE
##  [851,]  TRUE      TRUE       FALSE        FALSE
##  [852,]  TRUE      TRUE       FALSE        FALSE
##  [853,] FALSE      TRUE       FALSE        FALSE
##  [854,]  TRUE      TRUE       FALSE        FALSE
##  [855,]  TRUE      TRUE       FALSE        FALSE
##  [856,]  TRUE      TRUE       FALSE        FALSE
##  [857,]  TRUE      TRUE       FALSE        FALSE
##  [858,] FALSE     FALSE       FALSE        FALSE
##  [859,]  TRUE     FALSE       FALSE        FALSE
##  [860,] FALSE      TRUE       FALSE         TRUE
##  [861,]  TRUE      TRUE       FALSE         TRUE
##  [862,]  TRUE      TRUE        TRUE        FALSE
##  [863,] FALSE      TRUE       FALSE        FALSE
##  [864,] FALSE      TRUE       FALSE         TRUE
##  [865,]  TRUE      TRUE       FALSE         TRUE
##  [866,] FALSE     FALSE       FALSE        FALSE
##  [867,] FALSE      TRUE       FALSE        FALSE
##  [868,]  TRUE      TRUE       FALSE         TRUE
##  [869,]  TRUE     FALSE       FALSE         TRUE
##  [870,]  TRUE      TRUE       FALSE        FALSE
##  [871,] FALSE     FALSE       FALSE        FALSE
##  [872,]  TRUE     FALSE       FALSE        FALSE
##  [873,] FALSE     FALSE       FALSE        FALSE
##  [874,] FALSE      TRUE       FALSE        FALSE
##  [875,]  TRUE     FALSE       FALSE        FALSE
##  [876,]  TRUE      TRUE       FALSE        FALSE
##  [877,] FALSE     FALSE       FALSE        FALSE
##  [878,]  TRUE      TRUE       FALSE         TRUE
##  [879,]  TRUE     FALSE       FALSE        FALSE
##  [880,]  TRUE     FALSE        TRUE         TRUE
##  [881,]  TRUE     FALSE        TRUE         TRUE
##  [882,]  TRUE      TRUE       FALSE         TRUE
##  [883,]  TRUE      TRUE       FALSE         TRUE
##  [884,] FALSE      TRUE       FALSE        FALSE
##  [885,]  TRUE      TRUE        TRUE         TRUE
##  [886,] FALSE     FALSE       FALSE         TRUE
##  [887,]  TRUE      TRUE        TRUE        FALSE
##  [888,] FALSE     FALSE       FALSE        FALSE
##  [889,]  TRUE      TRUE       FALSE         TRUE
##  [890,]  TRUE      TRUE       FALSE         TRUE
##  [891,] FALSE      TRUE       FALSE         TRUE
##  [892,]  TRUE      TRUE       FALSE        FALSE
##  [893,]  TRUE      TRUE       FALSE         TRUE
##  [894,]  TRUE      TRUE       FALSE         TRUE
##  [895,]  TRUE      TRUE       FALSE         TRUE
##  [896,]  TRUE      TRUE       FALSE         TRUE
##  [897,]  TRUE      TRUE       FALSE         TRUE
##  [898,] FALSE      TRUE       FALSE        FALSE
##  [899,] FALSE      TRUE       FALSE        FALSE
##  [900,] FALSE      TRUE       FALSE        FALSE
##  [901,] FALSE      TRUE       FALSE        FALSE
##  [902,]  TRUE      TRUE        TRUE        FALSE
##  [903,]  TRUE      TRUE       FALSE         TRUE
##  [904,]  TRUE      TRUE       FALSE        FALSE
##  [905,]  TRUE      TRUE       FALSE        FALSE
##  [906,] FALSE      TRUE       FALSE         TRUE
##  [907,]  TRUE      TRUE       FALSE         TRUE
##  [908,]  TRUE      TRUE       FALSE         TRUE
##  [909,]  TRUE      TRUE       FALSE        FALSE
##  [910,] FALSE     FALSE       FALSE        FALSE
##  [911,] FALSE     FALSE       FALSE        FALSE
##  [912,] FALSE     FALSE       FALSE        FALSE
##  [913,]  TRUE      TRUE       FALSE         TRUE
##  [914,]  TRUE      TRUE       FALSE         TRUE
##  [915,]  TRUE      TRUE       FALSE         TRUE
##  [916,] FALSE      TRUE       FALSE        FALSE
##  [917,]  TRUE      TRUE       FALSE         TRUE
##  [918,] FALSE     FALSE       FALSE        FALSE
##  [919,] FALSE      TRUE        TRUE         TRUE
##  [920,] FALSE     FALSE       FALSE         TRUE
##  [921,] FALSE      TRUE       FALSE         TRUE
##  [922,] FALSE      TRUE       FALSE        FALSE
##  [923,] FALSE      TRUE       FALSE         TRUE
##  [924,] FALSE      TRUE       FALSE         TRUE
##  [925,]  TRUE      TRUE       FALSE         TRUE
##  [926,]  TRUE      TRUE       FALSE         TRUE
##  [927,]  TRUE      TRUE       FALSE         TRUE
##  [928,] FALSE      TRUE       FALSE         TRUE
##  [929,] FALSE      TRUE       FALSE         TRUE
##  [930,]  TRUE      TRUE       FALSE        FALSE
##  [931,]  TRUE      TRUE        TRUE        FALSE
##  [932,]  TRUE     FALSE       FALSE         TRUE
##  [933,]  TRUE      TRUE       FALSE        FALSE
##  [934,]  TRUE      TRUE        TRUE        FALSE
##  [935,] FALSE     FALSE       FALSE         TRUE
##  [936,]  TRUE      TRUE       FALSE         TRUE
##  [937,] FALSE      TRUE       FALSE        FALSE
##  [938,] FALSE      TRUE       FALSE        FALSE
##  [939,] FALSE     FALSE       FALSE        FALSE
##  [940,]  TRUE      TRUE       FALSE        FALSE
##  [941,] FALSE      TRUE       FALSE        FALSE
##  [942,]  TRUE     FALSE       FALSE         TRUE
##  [943,]  TRUE     FALSE       FALSE         TRUE
##  [944,]  TRUE      TRUE       FALSE         TRUE
##  [945,]  TRUE      TRUE       FALSE         TRUE
##  [946,] FALSE      TRUE       FALSE        FALSE
##  [947,]  TRUE      TRUE       FALSE         TRUE
##  [948,]  TRUE      TRUE       FALSE        FALSE
##  [949,] FALSE     FALSE       FALSE        FALSE
##  [950,] FALSE      TRUE       FALSE         TRUE
##  [951,]  TRUE      TRUE       FALSE         TRUE
##  [952,]  TRUE      TRUE       FALSE         TRUE
##  [953,]  TRUE      TRUE       FALSE         TRUE
##  [954,]  TRUE     FALSE       FALSE         TRUE
##  [955,]  TRUE      TRUE       FALSE         TRUE
##  [956,]  TRUE      TRUE       FALSE         TRUE
##  [957,]  TRUE      TRUE       FALSE         TRUE
##  [958,]  TRUE      TRUE       FALSE         TRUE
##  [959,]  TRUE      TRUE       FALSE         TRUE
##  [960,] FALSE     FALSE       FALSE         TRUE
##  [961,]  TRUE      TRUE        TRUE        FALSE
##  [962,] FALSE     FALSE       FALSE        FALSE
##  [963,] FALSE      TRUE       FALSE        FALSE
##  [964,]  TRUE      TRUE       FALSE        FALSE
##  [965,] FALSE      TRUE       FALSE         TRUE
##  [966,]  TRUE      TRUE       FALSE         TRUE
##  [967,] FALSE      TRUE       FALSE        FALSE
##  [968,] FALSE      TRUE       FALSE        FALSE
##  [969,]  TRUE      TRUE       FALSE         TRUE
##  [970,] FALSE      TRUE       FALSE         TRUE
##  [971,]  TRUE      TRUE       FALSE         TRUE
##  [972,]  TRUE      TRUE       FALSE         TRUE
##  [973,]  TRUE      TRUE       FALSE         TRUE
##  [974,]  TRUE      TRUE       FALSE         TRUE
##  [975,]  TRUE      TRUE       FALSE         TRUE
##  [976,]  TRUE      TRUE       FALSE         TRUE
##  [977,]  TRUE      TRUE       FALSE         TRUE
##  [978,] FALSE      TRUE       FALSE         TRUE
##  [979,]  TRUE      TRUE       FALSE         TRUE
##  [980,]  TRUE      TRUE       FALSE        FALSE
##  [981,] FALSE     FALSE       FALSE        FALSE
##  [982,]  TRUE      TRUE       FALSE         TRUE
##  [983,]  TRUE      TRUE       FALSE         TRUE
##  [984,] FALSE     FALSE       FALSE         TRUE
##  [985,]  TRUE      TRUE        TRUE        FALSE
##  [986,]  TRUE      TRUE       FALSE         TRUE
##  [987,]  TRUE      TRUE       FALSE         TRUE
##  [988,] FALSE      TRUE       FALSE         TRUE
##  [989,] FALSE      TRUE       FALSE         TRUE
##  [990,] FALSE      TRUE       FALSE        FALSE
##  [991,]  TRUE      TRUE       FALSE         TRUE
##  [992,] FALSE      TRUE       FALSE         TRUE
##  [993,]  TRUE      TRUE       FALSE         TRUE
##  [994,]  TRUE      TRUE        TRUE        FALSE
##  [995,]  TRUE      TRUE       FALSE        FALSE
##  [996,]  TRUE      TRUE       FALSE         TRUE
##  [997,] FALSE      TRUE       FALSE        FALSE
##  [998,] FALSE      TRUE       FALSE        FALSE
##  [999,] FALSE      TRUE       FALSE         TRUE
## [1000,] FALSE      TRUE       FALSE         TRUE
## [1001,] FALSE      TRUE       FALSE        FALSE
## [1002,] FALSE     FALSE       FALSE        FALSE
## [1003,] FALSE      TRUE       FALSE        FALSE
## [1004,]  TRUE      TRUE       FALSE         TRUE
## [1005,] FALSE     FALSE       FALSE        FALSE
## [1006,] FALSE      TRUE       FALSE         TRUE
## [1007,]  TRUE     FALSE       FALSE         TRUE
## [1008,]  TRUE      TRUE       FALSE         TRUE
## [1009,]  TRUE      TRUE       FALSE         TRUE
## [1010,] FALSE      TRUE       FALSE         TRUE
## [1011,]  TRUE      TRUE       FALSE         TRUE
## [1012,]  TRUE      TRUE       FALSE         TRUE
## [1013,] FALSE      TRUE       FALSE         TRUE
## [1014,] FALSE      TRUE       FALSE         TRUE
## [1015,] FALSE      TRUE       FALSE        FALSE
## [1016,]  TRUE      TRUE       FALSE         TRUE
## [1017,]  TRUE      TRUE       FALSE         TRUE
## [1018,]  TRUE      TRUE       FALSE         TRUE
## [1019,]  TRUE      TRUE       FALSE         TRUE
## [1020,]  TRUE      TRUE       FALSE         TRUE
## [1021,]  TRUE      TRUE       FALSE         TRUE
## [1022,]  TRUE      TRUE       FALSE         TRUE
## [1023,]  TRUE      TRUE       FALSE         TRUE
## [1024,] FALSE      TRUE       FALSE        FALSE
## [1025,]  TRUE      TRUE       FALSE         TRUE
## [1026,]  TRUE      TRUE       FALSE         TRUE
## [1027,] FALSE      TRUE       FALSE         TRUE
## [1028,] FALSE      TRUE       FALSE         TRUE
## [1029,]  TRUE      TRUE       FALSE         TRUE
## [1030,] FALSE     FALSE       FALSE        FALSE
## [1031,]  TRUE      TRUE       FALSE        FALSE
## [1032,]  TRUE      TRUE       FALSE        FALSE
## [1033,] FALSE      TRUE       FALSE        FALSE
## [1034,] FALSE     FALSE       FALSE        FALSE
## [1035,]  TRUE      TRUE       FALSE         TRUE
## [1036,] FALSE     FALSE       FALSE        FALSE
## [1037,] FALSE      TRUE       FALSE         TRUE
## [1038,]  TRUE      TRUE        TRUE        FALSE
## [1039,] FALSE     FALSE       FALSE        FALSE
## [1040,] FALSE     FALSE       FALSE        FALSE
## [1041,] FALSE      TRUE       FALSE         TRUE
## [1042,]  TRUE      TRUE       FALSE         TRUE
## [1043,]  TRUE     FALSE       FALSE         TRUE
## [1044,]  TRUE      TRUE       FALSE         TRUE
## [1045,]  TRUE      TRUE       FALSE         TRUE
## [1046,]  TRUE      TRUE       FALSE         TRUE
## [1047,]  TRUE      TRUE       FALSE         TRUE
## [1048,] FALSE      TRUE       FALSE         TRUE
## [1049,]  TRUE      TRUE       FALSE         TRUE
## [1050,]  TRUE      TRUE       FALSE         TRUE
## [1051,]  TRUE     FALSE       FALSE        FALSE
## [1052,]  TRUE      TRUE       FALSE        FALSE
## [1053,] FALSE      TRUE       FALSE        FALSE
## [1054,]  TRUE      TRUE       FALSE        FALSE
## [1055,]  TRUE      TRUE       FALSE        FALSE
## [1056,] FALSE     FALSE       FALSE        FALSE
## [1057,] FALSE      TRUE       FALSE        FALSE
## [1058,]  TRUE      TRUE        TRUE        FALSE
## [1059,] FALSE     FALSE       FALSE        FALSE
## [1060,] FALSE     FALSE       FALSE        FALSE
## [1061,] FALSE     FALSE       FALSE        FALSE
## [1062,] FALSE     FALSE       FALSE        FALSE
## [1063,] FALSE     FALSE       FALSE        FALSE
## [1064,] FALSE      TRUE       FALSE        FALSE
## [1065,] FALSE      TRUE       FALSE        FALSE
## [1066,] FALSE      TRUE       FALSE        FALSE
## [1067,] FALSE      TRUE       FALSE        FALSE
## [1068,] FALSE     FALSE       FALSE        FALSE
## [1069,] FALSE      TRUE       FALSE        FALSE
## [1070,] FALSE      TRUE       FALSE        FALSE
## [1071,]  TRUE      TRUE        TRUE         TRUE
## [1072,] FALSE      TRUE        TRUE         TRUE
## [1073,]  TRUE      TRUE       FALSE         TRUE
## [1074,]  TRUE      TRUE       FALSE         TRUE
## [1075,]  TRUE      TRUE       FALSE         TRUE
## [1076,]  TRUE      TRUE       FALSE         TRUE
## [1077,]  TRUE      TRUE       FALSE         TRUE
## [1078,] FALSE      TRUE       FALSE        FALSE
## [1079,] FALSE     FALSE       FALSE        FALSE
## [1080,] FALSE     FALSE       FALSE        FALSE
## [1081,]  TRUE      TRUE       FALSE         TRUE
## [1082,]  TRUE      TRUE       FALSE         TRUE
## [1083,] FALSE      TRUE       FALSE         TRUE
## [1084,] FALSE     FALSE       FALSE        FALSE
## [1085,]  TRUE      TRUE       FALSE        FALSE
## [1086,] FALSE     FALSE       FALSE         TRUE
## [1087,]  TRUE      TRUE       FALSE        FALSE
## [1088,] FALSE      TRUE       FALSE        FALSE
## [1089,]  TRUE      TRUE       FALSE         TRUE
## [1090,]  TRUE      TRUE       FALSE         TRUE
## [1091,] FALSE      TRUE       FALSE        FALSE
## [1092,] FALSE      TRUE       FALSE         TRUE
## [1093,] FALSE      TRUE       FALSE        FALSE
## [1094,] FALSE      TRUE       FALSE        FALSE
## [1095,] FALSE     FALSE       FALSE        FALSE
## [1096,]  TRUE      TRUE       FALSE        FALSE
## [1097,] FALSE      TRUE       FALSE        FALSE
## [1098,] FALSE     FALSE       FALSE        FALSE
## [1099,] FALSE     FALSE       FALSE        FALSE
## [1100,] FALSE     FALSE       FALSE        FALSE
## [1101,] FALSE     FALSE       FALSE        FALSE
## [1102,] FALSE      TRUE       FALSE        FALSE
## [1103,]  TRUE      TRUE       FALSE         TRUE
## [1104,]  TRUE      TRUE        TRUE         TRUE
## [1105,] FALSE      TRUE       FALSE         TRUE
## [1106,] FALSE      TRUE       FALSE         TRUE
## [1107,]  TRUE      TRUE       FALSE         TRUE
## [1108,] FALSE      TRUE       FALSE         TRUE
## [1109,] FALSE     FALSE       FALSE         TRUE
## [1110,]  TRUE     FALSE       FALSE         TRUE
## [1111,]  TRUE     FALSE       FALSE         TRUE
## [1112,] FALSE      TRUE       FALSE        FALSE
## [1113,] FALSE      TRUE       FALSE         TRUE
## [1114,] FALSE      TRUE       FALSE         TRUE
## [1115,]  TRUE      TRUE       FALSE         TRUE
## [1116,]  TRUE      TRUE       FALSE         TRUE
## [1117,] FALSE     FALSE       FALSE        FALSE
## [1118,] FALSE     FALSE       FALSE        FALSE
## [1119,] FALSE     FALSE       FALSE        FALSE
## [1120,]  TRUE      TRUE       FALSE         TRUE
## [1121,] FALSE      TRUE       FALSE        FALSE
## [1122,] FALSE      TRUE       FALSE        FALSE
## [1123,] FALSE      TRUE       FALSE         TRUE
## [1124,]  TRUE      TRUE       FALSE         TRUE
## [1125,]  TRUE      TRUE       FALSE         TRUE
## [1126,]  TRUE      TRUE       FALSE         TRUE
## [1127,] FALSE     FALSE       FALSE        FALSE
## [1128,] FALSE      TRUE       FALSE         TRUE
## [1129,] FALSE      TRUE       FALSE         TRUE
## [1130,]  TRUE     FALSE       FALSE         TRUE
## [1131,] FALSE      TRUE        TRUE         TRUE
## [1132,]  TRUE      TRUE       FALSE         TRUE
## [1133,]  TRUE      TRUE       FALSE         TRUE
## [1134,] FALSE      TRUE        TRUE         TRUE
## [1135,] FALSE      TRUE       FALSE         TRUE
## [1136,]  TRUE      TRUE       FALSE         TRUE
## [1137,]  TRUE      TRUE       FALSE         TRUE
## [1138,]  TRUE      TRUE       FALSE         TRUE
## [1139,]  TRUE      TRUE       FALSE         TRUE
## [1140,]  TRUE      TRUE       FALSE         TRUE
## [1141,]  TRUE      TRUE       FALSE         TRUE
## [1142,]  TRUE      TRUE       FALSE         TRUE
## [1143,] FALSE      TRUE       FALSE         TRUE
## [1144,]  TRUE      TRUE       FALSE         TRUE
## [1145,] FALSE      TRUE       FALSE         TRUE
## [1146,]  TRUE      TRUE       FALSE         TRUE
## [1147,]  TRUE      TRUE       FALSE         TRUE
## [1148,]  TRUE      TRUE       FALSE         TRUE
## [1149,]  TRUE      TRUE        TRUE        FALSE
## [1150,] FALSE      TRUE       FALSE        FALSE
## [1151,] FALSE      TRUE       FALSE        FALSE
## [1152,] FALSE      TRUE       FALSE        FALSE
## [1153,] FALSE     FALSE       FALSE        FALSE
## [1154,] FALSE      TRUE       FALSE        FALSE
## [1155,] FALSE      TRUE       FALSE        FALSE
## [1156,] FALSE      TRUE       FALSE        FALSE
## [1157,]  TRUE      TRUE       FALSE        FALSE
## [1158,]  TRUE      TRUE       FALSE        FALSE
## [1159,] FALSE     FALSE       FALSE         TRUE
## [1160,] FALSE     FALSE       FALSE        FALSE
## [1161,] FALSE      TRUE        TRUE        FALSE
## [1162,] FALSE     FALSE       FALSE        FALSE
## [1163,] FALSE      TRUE       FALSE        FALSE
## [1164,]  TRUE      TRUE        TRUE         TRUE
## [1165,]  TRUE      TRUE       FALSE         TRUE
## [1166,]  TRUE     FALSE       FALSE         TRUE
## [1167,]  TRUE      TRUE       FALSE         TRUE
## [1168,]  TRUE      TRUE       FALSE         TRUE
## [1169,]  TRUE      TRUE       FALSE         TRUE
## [1170,]  TRUE     FALSE       FALSE         TRUE
## [1171,] FALSE      TRUE       FALSE         TRUE
## [1172,] FALSE      TRUE       FALSE         TRUE
## [1173,] FALSE     FALSE       FALSE         TRUE
## [1174,]  TRUE      TRUE       FALSE         TRUE
## [1175,] FALSE      TRUE       FALSE         TRUE
## [1176,]  TRUE      TRUE       FALSE         TRUE
## [1177,]  TRUE      TRUE       FALSE         TRUE
## [1178,] FALSE      TRUE       FALSE         TRUE
## [1179,]  TRUE      TRUE       FALSE         TRUE
## [1180,]  TRUE      TRUE       FALSE         TRUE
## [1181,]  TRUE      TRUE       FALSE         TRUE
## [1182,] FALSE      TRUE       FALSE         TRUE
## [1183,] FALSE      TRUE       FALSE         TRUE
## [1184,] FALSE     FALSE       FALSE         TRUE
## [1185,]  TRUE      TRUE       FALSE         TRUE
## [1186,]  TRUE      TRUE       FALSE         TRUE
## [1187,] FALSE      TRUE       FALSE        FALSE
## [1188,] FALSE      TRUE       FALSE        FALSE
## [1189,] FALSE     FALSE       FALSE        FALSE
## [1190,]  TRUE      TRUE       FALSE        FALSE
## [1191,]  TRUE      TRUE       FALSE        FALSE
## [1192,] FALSE      TRUE       FALSE        FALSE
## [1193,] FALSE      TRUE        TRUE         TRUE
## [1194,] FALSE      TRUE        TRUE         TRUE
## [1195,]  TRUE      TRUE       FALSE         TRUE
## [1196,] FALSE      TRUE       FALSE         TRUE
## [1197,]  TRUE      TRUE       FALSE         TRUE
## [1198,]  TRUE      TRUE       FALSE         TRUE
## [1199,] FALSE      TRUE       FALSE         TRUE
## [1200,] FALSE      TRUE       FALSE         TRUE
## [1201,] FALSE      TRUE       FALSE         TRUE
## [1202,] FALSE     FALSE       FALSE        FALSE
## [1203,] FALSE      TRUE       FALSE        FALSE
## [1204,] FALSE      TRUE       FALSE        FALSE
## [1205,] FALSE      TRUE       FALSE        FALSE
## [1206,] FALSE     FALSE       FALSE        FALSE
## [1207,] FALSE      TRUE       FALSE         TRUE
## [1208,] FALSE     FALSE       FALSE         TRUE
## [1209,] FALSE      TRUE       FALSE         TRUE
## [1210,] FALSE      TRUE       FALSE         TRUE
## [1211,]  TRUE      TRUE       FALSE        FALSE
## [1212,]  TRUE      TRUE       FALSE        FALSE
## [1213,] FALSE     FALSE       FALSE        FALSE
## [1214,]  TRUE      TRUE       FALSE         TRUE
## [1215,]  TRUE      TRUE       FALSE        FALSE
## [1216,]  TRUE      TRUE        TRUE         TRUE
## [1217,]  TRUE      TRUE        TRUE         TRUE
## [1218,] FALSE     FALSE       FALSE        FALSE
## [1219,] FALSE     FALSE       FALSE         TRUE
## [1220,] FALSE      TRUE       FALSE         TRUE
## [1221,] FALSE      TRUE       FALSE         TRUE
## [1222,]  TRUE     FALSE       FALSE         TRUE
## [1223,]  TRUE      TRUE        TRUE         TRUE
## [1224,] FALSE      TRUE       FALSE         TRUE
## [1225,] FALSE      TRUE       FALSE         TRUE
## [1226,] FALSE     FALSE       FALSE        FALSE
## [1227,] FALSE      TRUE       FALSE        FALSE
## [1228,]  TRUE      TRUE       FALSE        FALSE
## [1229,]  TRUE      TRUE       FALSE         TRUE
## [1230,]  TRUE      TRUE       FALSE         TRUE
## [1231,]  TRUE      TRUE       FALSE         TRUE
## [1232,]  TRUE     FALSE       FALSE         TRUE
## [1233,]  TRUE     FALSE       FALSE        FALSE
## [1234,]  TRUE      TRUE       FALSE        FALSE
## [1235,]  TRUE      TRUE       FALSE         TRUE
## [1236,] FALSE     FALSE       FALSE        FALSE
## [1237,]  TRUE      TRUE        TRUE         TRUE
## [1238,] FALSE      TRUE        TRUE         TRUE
## [1239,]  TRUE      TRUE       FALSE         TRUE
## [1240,]  TRUE      TRUE       FALSE         TRUE
## [1241,] FALSE      TRUE       FALSE        FALSE
## [1242,] FALSE      TRUE       FALSE        FALSE
## [1243,] FALSE     FALSE       FALSE        FALSE
## [1244,]  TRUE      TRUE        TRUE        FALSE
## [1245,] FALSE      TRUE        TRUE        FALSE
## [1246,]  TRUE      TRUE       FALSE         TRUE
## [1247,] FALSE      TRUE       FALSE         TRUE
## [1248,] FALSE      TRUE       FALSE         TRUE
## [1249,] FALSE      TRUE       FALSE         TRUE
## [1250,] FALSE      TRUE        TRUE         TRUE
## [1251,] FALSE     FALSE       FALSE         TRUE
## [1252,]  TRUE      TRUE       FALSE         TRUE
## [1253,] FALSE      TRUE       FALSE         TRUE
## [1254,]  TRUE      TRUE       FALSE         TRUE
## [1255,]  TRUE      TRUE       FALSE         TRUE
## [1256,]  TRUE      TRUE       FALSE         TRUE
## [1257,] FALSE      TRUE       FALSE        FALSE
## [1258,]  TRUE      TRUE       FALSE         TRUE
## [1259,]  TRUE      TRUE       FALSE         TRUE
## [1260,]  TRUE      TRUE        TRUE        FALSE
## [1261,] FALSE     FALSE       FALSE        FALSE
## [1262,]  TRUE      TRUE        TRUE        FALSE
## [1263,]  TRUE     FALSE       FALSE        FALSE
## [1264,] FALSE     FALSE       FALSE        FALSE
## [1265,]  TRUE      TRUE       FALSE        FALSE
## [1266,]  TRUE      TRUE       FALSE         TRUE
## [1267,]  TRUE     FALSE       FALSE        FALSE
## [1268,]  TRUE      TRUE       FALSE        FALSE
## [1269,] FALSE     FALSE       FALSE        FALSE
## [1270,] FALSE     FALSE       FALSE        FALSE
## [1271,] FALSE     FALSE       FALSE        FALSE
## [1272,]  TRUE     FALSE       FALSE         TRUE
## [1273,]  TRUE     FALSE       FALSE        FALSE
## [1274,] FALSE     FALSE       FALSE        FALSE
## [1275,]  TRUE      TRUE       FALSE        FALSE
## [1276,] FALSE      TRUE       FALSE        FALSE
## [1277,]  TRUE     FALSE       FALSE        FALSE
## [1278,]  TRUE      TRUE       FALSE        FALSE
## [1279,] FALSE     FALSE       FALSE        FALSE
## [1280,]  TRUE      TRUE       FALSE         TRUE
## [1281,]  TRUE      TRUE       FALSE         TRUE
## [1282,]  TRUE      TRUE       FALSE         TRUE
## [1283,]  TRUE     FALSE       FALSE         TRUE
## [1284,] FALSE     FALSE       FALSE        FALSE
## [1285,] FALSE      TRUE       FALSE        FALSE
## [1286,] FALSE      TRUE       FALSE        FALSE
## [1287,] FALSE      TRUE       FALSE        FALSE
## [1288,]  TRUE      TRUE       FALSE        FALSE
## [1289,]  TRUE     FALSE       FALSE         TRUE
## [1290,]  TRUE      TRUE       FALSE         TRUE
## [1291,]  TRUE      TRUE       FALSE         TRUE
## [1292,]  TRUE      TRUE       FALSE         TRUE
## [1293,]  TRUE      TRUE       FALSE         TRUE
## [1294,] FALSE      TRUE       FALSE        FALSE
## [1295,]  TRUE      TRUE       FALSE         TRUE
## [1296,]  TRUE      TRUE       FALSE         TRUE
## [1297,]  TRUE      TRUE       FALSE         TRUE
## [1298,]  TRUE     FALSE       FALSE        FALSE
## [1299,] FALSE     FALSE       FALSE        FALSE
## [1300,] FALSE     FALSE       FALSE        FALSE
## [1301,]  TRUE      TRUE       FALSE         TRUE
## [1302,]  TRUE      TRUE       FALSE         TRUE
## [1303,]  TRUE      TRUE       FALSE        FALSE
## [1304,]  TRUE      TRUE       FALSE         TRUE
## [1305,]  TRUE      TRUE       FALSE         TRUE
## [1306,] FALSE      TRUE       FALSE         TRUE
## [1307,] FALSE      TRUE       FALSE        FALSE
## [1308,] FALSE      TRUE       FALSE        FALSE
## [1309,] FALSE      TRUE       FALSE        FALSE
## [1310,] FALSE     FALSE       FALSE        FALSE
## [1311,] FALSE      TRUE       FALSE        FALSE
## [1312,]  TRUE      TRUE        TRUE        FALSE
## [1313,] FALSE     FALSE       FALSE        FALSE
## [1314,]  TRUE      TRUE       FALSE        FALSE
## [1315,] FALSE     FALSE       FALSE        FALSE
## [1316,]  TRUE      TRUE       FALSE        FALSE
## [1317,]  TRUE      TRUE       FALSE        FALSE
## [1318,]  TRUE      TRUE       FALSE         TRUE
## [1319,]  TRUE      TRUE       FALSE         TRUE
## [1320,] FALSE      TRUE       FALSE         TRUE
## [1321,]  TRUE      TRUE       FALSE         TRUE
## [1322,]  TRUE      TRUE       FALSE        FALSE
## [1323,] FALSE      TRUE       FALSE        FALSE
## [1324,]  TRUE      TRUE        TRUE         TRUE
## [1325,]  TRUE      TRUE       FALSE         TRUE
## [1326,]  TRUE      TRUE       FALSE         TRUE
## [1327,]  TRUE      TRUE       FALSE         TRUE
## [1328,]  TRUE     FALSE       FALSE         TRUE
## [1329,]  TRUE      TRUE       FALSE         TRUE
## [1330,]  TRUE      TRUE       FALSE         TRUE
## [1331,]  TRUE      TRUE       FALSE         TRUE
## [1332,]  TRUE      TRUE       FALSE         TRUE
## [1333,]  TRUE      TRUE       FALSE        FALSE
## [1334,]  TRUE     FALSE       FALSE         TRUE
## [1335,] FALSE      TRUE       FALSE        FALSE
## [1336,] FALSE      TRUE       FALSE        FALSE
## [1337,]  TRUE      TRUE       FALSE         TRUE
## [1338,]  TRUE      TRUE       FALSE         TRUE
## [1339,]  TRUE      TRUE       FALSE         TRUE
## [1340,]  TRUE      TRUE       FALSE         TRUE
## [1341,]  TRUE      TRUE       FALSE         TRUE
## [1342,]  TRUE      TRUE       FALSE        FALSE
## [1343,] FALSE     FALSE       FALSE        FALSE
## [1344,] FALSE      TRUE       FALSE        FALSE
## [1345,] FALSE      TRUE       FALSE        FALSE
## [1346,] FALSE     FALSE       FALSE        FALSE
## [1347,]  TRUE     FALSE       FALSE        FALSE
## [1348,] FALSE     FALSE       FALSE        FALSE
## [1349,]  TRUE      TRUE       FALSE        FALSE
## [1350,] FALSE      TRUE        TRUE         TRUE
## [1351,] FALSE      TRUE       FALSE         TRUE
## [1352,]  TRUE      TRUE       FALSE         TRUE
## [1353,]  TRUE      TRUE        TRUE        FALSE
## [1354,] FALSE     FALSE       FALSE        FALSE
## [1355,] FALSE     FALSE       FALSE        FALSE
## [1356,] FALSE      TRUE       FALSE        FALSE
## [1357,]  TRUE      TRUE        TRUE        FALSE
## [1358,]  TRUE      TRUE        TRUE        FALSE
## [1359,]  TRUE      TRUE        TRUE        FALSE
## [1360,]  TRUE      TRUE        TRUE        FALSE
## [1361,] FALSE      TRUE        TRUE        FALSE
## [1362,]  TRUE      TRUE       FALSE        FALSE
## [1363,] FALSE      TRUE       FALSE        FALSE
## [1364,] FALSE     FALSE       FALSE        FALSE
## [1365,]  TRUE      TRUE       FALSE        FALSE
## [1366,] FALSE      TRUE       FALSE        FALSE
## [1367,]  TRUE      TRUE       FALSE        FALSE
## [1368,] FALSE      TRUE       FALSE        FALSE
## [1369,]  TRUE      TRUE       FALSE        FALSE
## [1370,] FALSE      TRUE       FALSE        FALSE
## [1371,]  TRUE      TRUE       FALSE        FALSE
## [1372,] FALSE      TRUE       FALSE        FALSE
## [1373,] FALSE     FALSE       FALSE        FALSE
## [1374,] FALSE      TRUE       FALSE        FALSE
## [1375,] FALSE      TRUE       FALSE        FALSE
## [1376,] FALSE      TRUE       FALSE        FALSE
## [1377,] FALSE      TRUE       FALSE        FALSE
## [1378,] FALSE      TRUE       FALSE        FALSE
## [1379,]  TRUE      TRUE       FALSE        FALSE
## [1380,] FALSE      TRUE       FALSE        FALSE
## [1381,]  TRUE      TRUE       FALSE         TRUE
## [1382,]  TRUE      TRUE       FALSE         TRUE
## [1383,]  TRUE      TRUE       FALSE         TRUE
## [1384,]  TRUE      TRUE       FALSE         TRUE
## [1385,]  TRUE      TRUE       FALSE         TRUE
## [1386,]  TRUE      TRUE       FALSE        FALSE
## [1387,]  TRUE      TRUE       FALSE        FALSE
## [1388,] FALSE     FALSE       FALSE        FALSE
## [1389,]  TRUE      TRUE       FALSE        FALSE
## [1390,] FALSE      TRUE       FALSE        FALSE
## [1391,] FALSE     FALSE       FALSE        FALSE
## [1392,]  TRUE      TRUE       FALSE        FALSE
## [1393,]  TRUE      TRUE       FALSE        FALSE
## [1394,]  TRUE      TRUE       FALSE        FALSE
## [1395,] FALSE      TRUE       FALSE        FALSE
## [1396,] FALSE     FALSE       FALSE        FALSE
## [1397,] FALSE      TRUE       FALSE        FALSE
## [1398,] FALSE      TRUE       FALSE        FALSE
## [1399,]  TRUE      TRUE       FALSE        FALSE
## [1400,] FALSE     FALSE       FALSE        FALSE
## [1401,] FALSE      TRUE       FALSE        FALSE
## [1402,] FALSE     FALSE       FALSE        FALSE
## [1403,] FALSE     FALSE       FALSE         TRUE
## [1404,] FALSE     FALSE       FALSE         TRUE
## [1405,] FALSE     FALSE       FALSE        FALSE
## [1406,]  TRUE      TRUE        TRUE        FALSE
## [1407,] FALSE     FALSE       FALSE        FALSE
## [1408,]  TRUE      TRUE       FALSE         TRUE
## [1409,]  TRUE      TRUE       FALSE         TRUE
## [1410,]  TRUE      TRUE       FALSE         TRUE
## [1411,] FALSE     FALSE       FALSE         TRUE
## [1412,]  TRUE      TRUE       FALSE        FALSE
## [1413,]  TRUE     FALSE       FALSE         TRUE
## [1414,]  TRUE     FALSE       FALSE         TRUE
## [1415,] FALSE     FALSE       FALSE        FALSE
## [1416,]  TRUE      TRUE       FALSE         TRUE
## [1417,]  TRUE      TRUE       FALSE         TRUE
## [1418,] FALSE     FALSE       FALSE        FALSE
## [1419,] FALSE     FALSE       FALSE        FALSE
## [1420,] FALSE     FALSE       FALSE        FALSE
## [1421,]  TRUE     FALSE       FALSE         TRUE
## [1422,] FALSE      TRUE       FALSE        FALSE
## [1423,] FALSE     FALSE       FALSE        FALSE
## [1424,]  TRUE      TRUE       FALSE         TRUE
## [1425,]  TRUE      TRUE       FALSE         TRUE
## [1426,] FALSE     FALSE       FALSE        FALSE
## [1427,]  TRUE      TRUE       FALSE         TRUE
## [1428,]  TRUE      TRUE       FALSE         TRUE
## [1429,]  TRUE      TRUE       FALSE         TRUE
## [1430,] FALSE      TRUE       FALSE         TRUE
## [1431,] FALSE     FALSE       FALSE        FALSE
## [1432,] FALSE     FALSE       FALSE         TRUE
## [1433,] FALSE     FALSE       FALSE         TRUE
## [1434,]  TRUE      TRUE       FALSE        FALSE
## [1435,] FALSE     FALSE       FALSE         TRUE
## [1436,] FALSE     FALSE       FALSE        FALSE
## [1437,] FALSE     FALSE       FALSE        FALSE
## [1438,]  TRUE      TRUE       FALSE         TRUE
## [1439,] FALSE     FALSE       FALSE        FALSE
## [1440,] FALSE     FALSE       FALSE        FALSE
```


4. In which variable are the most NAs in the data frame?

```r
mammal_hist_NA %>%
  purrr::map_df(~ sum(is.na(.))) %>% 
  pivot_longer(everything(),
    names_to= "variables",
    values_to = "NAsum") %>% 
  arrange(desc(NAsum))
```

```
## # A tibble: 13 x 2
##    variables    NAsum
##    <chr>        <int>
##  1 wean mass     1039
##  2 max. life      841
##  3 litters/year   689
##  4 weaning        619
##  5 AFR            607
##  6 newborn        595
##  7 gestation      418
##  8 mass            85
##  9 litter size     84
## 10 order            0
## 11 family           0
## 12 Genus            0
## 13 species          0
```


## That's it! Take a break and I will see you on Zoom!  

-->[Home](https://jmledford3115.github.io/datascibiol/)
