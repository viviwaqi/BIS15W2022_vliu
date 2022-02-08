---
title: "Lab 9 Homework"
author: "Victoria Liu"
date: "2022-02-08"
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
library(here)
library(naniar)
```

For this homework, we will take a departure from biological data and use data about California colleges. These data are a subset of the national college scorecard (https://collegescorecard.ed.gov/data/). Load the `ca_college_data.csv` as a new object called `colleges`.

```r
colleges<- readr::read_csv("data/ca_college_data.csv")
```

```
## Rows: 341 Columns: 10
## -- Column specification --------------------------------------------------------
## Delimiter: ","
## chr (4): INSTNM, CITY, STABBR, ZIP
## dbl (6): ADM_RATE, SAT_AVG, PCIP26, COSTT4_A, C150_4_POOLED, PFTFTUG1_EF
## 
## i Use `spec()` to retrieve the full column specification for this data.
## i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
colleges
```

```
## # A tibble: 341 x 10
##    INSTNM      CITY  STABBR ZIP   ADM_RATE SAT_AVG PCIP26 COSTT4_A C150_4_POOLED
##    <chr>       <chr> <chr>  <chr>    <dbl>   <dbl>  <dbl>    <dbl>         <dbl>
##  1 Grossmont ~ El C~ CA     9202~       NA      NA 0.0016     7956        NA    
##  2 College of~ Visa~ CA     9327~       NA      NA 0.0066     8109        NA    
##  3 College of~ San ~ CA     9440~       NA      NA 0.0038     8278        NA    
##  4 Ventura Co~ Vent~ CA     9300~       NA      NA 0.0035     8407        NA    
##  5 Oxnard Col~ Oxna~ CA     9303~       NA      NA 0.0085     8516        NA    
##  6 Moorpark C~ Moor~ CA     9302~       NA      NA 0.0151     8577        NA    
##  7 Skyline Co~ San ~ CA     9406~       NA      NA 0          8580         0.233
##  8 Glendale C~ Glen~ CA     9120~       NA      NA 0.002      9181        NA    
##  9 Citrus Col~ Glen~ CA     9174~       NA      NA 0.0021     9281        NA    
## 10 Fresno Cit~ Fres~ CA     93741       NA      NA 0.0324     9370        NA    
## # ... with 331 more rows, and 1 more variable: PFTFTUG1_EF <dbl>
```

The variables are a bit hard to decipher, here is a key:  

INSTNM: Institution name  
CITY: California city  
STABBR: Location state  
ZIP: Zip code  
ADM_RATE: Admission rate  
SAT_AVG: SAT average score  
PCIP26: Percentage of degrees awarded in Biological And Biomedical Sciences  
COSTT4_A: Annual cost of attendance  
C150_4_POOLED: 4-year completion rate  
PFTFTUG1_EF: Percentage of undergraduate students who are first-time, full-time degree/certificate-seeking undergraduate students  

1. Use your preferred function(s) to have a look at the data and get an idea of its structure. Make sure you summarize NA's and determine whether or not the data are tidy. You may also consider dealing with any naming issues.

```r
colleges<- clean_names(colleges)
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
names(colleges)
```

```
##  [1] "instnm"        "city"          "stabbr"        "zip"          
##  [5] "adm_rate"      "sat_avg"       "pcip26"        "costt4_a"     
##  [9] "c150_4_pooled" "pftftug1_ef"
```


```r
glimpse(colleges)
```

```
## Rows: 341
## Columns: 10
## $ instnm        <chr> "Grossmont College", "College of the Sequoias", "College~
## $ city          <chr> "El Cajon", "Visalia", "San Mateo", "Ventura", "Oxnard",~
## $ stabbr        <chr> "CA", "CA", "CA", "CA", "CA", "CA", "CA", "CA", "CA", "C~
## $ zip           <chr> "92020-1799", "93277-2214", "94402-3784", "93003-3872", ~
## $ adm_rate      <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ sat_avg       <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ pcip26        <dbl> 0.0016, 0.0066, 0.0038, 0.0035, 0.0085, 0.0151, 0.0000, ~
## $ costt4_a      <dbl> 7956, 8109, 8278, 8407, 8516, 8577, 8580, 9181, 9281, 93~
## $ c150_4_pooled <dbl> NA, NA, NA, NA, NA, NA, 0.2334, NA, NA, NA, NA, 0.1704, ~
## $ pftftug1_ef   <dbl> 0.3546, 0.5413, 0.3567, 0.3824, 0.2753, 0.4286, 0.2307, ~
```


```r
miss_var_summary(colleges)
```

```
## # A tibble: 10 x 3
##    variable      n_miss pct_miss
##    <chr>          <int>    <dbl>
##  1 sat_avg          276     80.9
##  2 adm_rate         240     70.4
##  3 c150_4_pooled    221     64.8
##  4 costt4_a         124     36.4
##  5 pftftug1_ef       53     15.5
##  6 pcip26            35     10.3
##  7 instnm             0      0  
##  8 city               0      0  
##  9 stabbr             0      0  
## 10 zip                0      0
```

```r
#The data is tidy
```

2. Which cities in California have the highest number of colleges?

```r
colleges_counts<- colleges %>%
  select(city, instnm) %>%
  add_count(city) %>%
  arrange(desc(n))
summarize(colleges_counts, city, instnm, n)
```

```
## # A tibble: 341 x 3
##    city        instnm                                                          n
##    <chr>       <chr>                                                       <int>
##  1 Los Angeles Los Angeles Southwest College                                  24
##  2 Los Angeles Los Angeles City College                                       24
##  3 Los Angeles Los Angeles Trade Technical College                            24
##  4 Los Angeles California State University-Los Angeles                        24
##  5 Los Angeles Yeshiva Ohr Elchonon Chabad West Coast Talmudical Seminary     24
##  6 Los Angeles University of California-Los Angeles                           24
##  7 Los Angeles American Jewish University                                     24
##  8 Los Angeles FIDM-Fashion Institute of Design & Merchandising-Los Angel~    24
##  9 Los Angeles Mount Saint Mary's University                                  24
## 10 Los Angeles American Academy of Dramatic Arts-Los Angeles                  24
## # ... with 331 more rows
```

```r
#i wasn't able to figure out the ggplot using pivot wider so i used distinct
colleges_counts %>%
  pivot_wider(-instnm,
              names_from = city,
              values_from = n)
```

```
## Warning: Values from `n` are not uniquely identified; output will contain list-cols.
## * Use `values_fn = list` to suppress this warning.
## * Use `values_fn = {summary_fun}` to summarise duplicates.
## * Use the following dplyr code to identify duplicates.
##   {data} %>%
##     dplyr::group_by(city) %>%
##     dplyr::summarise(n = dplyr::n(), .groups = "drop") %>%
##     dplyr::filter(n > 1L)
```

```
## # A tibble: 1 x 161
##   `Los Angeles` `San Diego` `San Francisco` Sacramento Berkeley  Oakland  
##   <list>        <list>      <list>          <list>     <list>    <list>   
## 1 <int [24]>    <int [18]>  <int [15]>      <int [10]> <int [9]> <int [9]>
## # ... with 155 more variables: Claremont <list>, Pasadena <list>,
## #   Fresno <list>, Irvine <list>, Riverside <list>, `San Jose` <list>,
## #   `El Cajon` <list>, `Santa Monica` <list>, Fullerton <list>, Redding <list>,
## #   `Santa Barbara` <list>, Stockton <list>, Anaheim <list>, Visalia <list>,
## #   Ventura <list>, Bakersfield <list>, Oceanside <list>, Hayward <list>,
## #   Whittier <list>, Alameda <list>, `Santa Rosa` <list>,
## #   `San Bernardino` <list>, Modesto <list>, `Santa Clara` <list>, ...
```


```r
colleges_counts<- colleges_counts %>%
  distinct(n, .keep_all = TRUE)
colleges_counts
```

```
## # A tibble: 12 x 3
##    city          instnm                            n
##    <chr>         <chr>                         <int>
##  1 Los Angeles   Los Angeles Southwest College    24
##  2 San Diego     San Diego Miramar College        18
##  3 San Francisco City College of San Francisco    15
##  4 Sacramento    American River College           10
##  5 Berkeley      Berkeley City College             9
##  6 Claremont     Pomona College                    7
##  7 Pasadena      Pasadena City College             6
##  8 Fresno        Fresno City College               5
##  9 El Cajon      Grossmont College                 4
## 10 Visalia       College of the Sequoias           3
## 11 Oxnard        Oxnard College                    2
## 12 San Mateo     College of San Mateo              1
```

3. Based on your answer to #2, make a plot that shows the number of colleges in the top 10 cities.

```r
colleges_counts %>%
  slice_max(order_by = n, n = 10) %>%
  ggplot(aes(x=n, y=city))+
  geom_col()
```

![](lab9_hw_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

4. The column `COSTT4_A` is the annual cost of each institution. Which city has the highest average cost? Where is it located?

```r
colleges %>%
  select(city, costt4_a) %>%
  na.omit() %>%
  group_by(city) %>%
  mutate(mean_cost=mean(costt4_a)) %>%
  arrange(desc(mean_cost)) 
```

```
## # A tibble: 217 x 3
## # Groups:   city [132]
##    city      costt4_a mean_cost
##    <chr>        <dbl>     <dbl>
##  1 Claremont    64870     66498
##  2 Claremont    65880     66498
##  3 Claremont    66060     66498
##  4 Claremont    66325     66498
##  5 Claremont    69355     66498
##  6 Malibu       66152     66152
##  7 Valencia     64686     64686
##  8 Orange       64501     64501
##  9 Redlands     61542     61542
## 10 Moraga       61095     61095
## # ... with 207 more rows
```

5. Based on your answer to #4, make a plot that compares the cost of the individual colleges in the most expensive city. Bonus! Add UC Davis here to see how it compares :>).

```r
colleges %>%
  filter(city == "Claremont") %>%
  ggplot(aes(x=costt4_a, y=instnm))+
  geom_col()
```

```
## Warning: Removed 2 rows containing missing values (position_stack).
```

![](lab9_hw_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

6. The column `ADM_RATE` is the admissions rate by college and `C150_4_POOLED` is the four-year completion rate. Use a scatterplot to show the relationship between these two variables. What do you think this means?

```r
colleges %>%
  ggplot(aes(x=adm_rate, y=c150_4_pooled))+
  geom_point()
```

```
## Warning: Removed 251 rows containing missing values (geom_point).
```

![](lab9_hw_files/figure-html/unnamed-chunk-12-1.png)<!-- -->

7. Is there a relationship between cost and four-year completion rate? (You don't need to do the stats, just produce a plot). What do you think this means?

```r
colleges %>%
  ggplot(aes(x=c150_4_pooled, y=costt4_a))+
  geom_point()
```

```
## Warning: Removed 225 rows containing missing values (geom_point).
```

![](lab9_hw_files/figure-html/unnamed-chunk-13-1.png)<!-- -->

8. The column titled `INSTNM` is the institution name. We are only interested in the University of California colleges. Make a new data frame that is restricted to UC institutions. You can remove `Hastings College of Law` and `UC San Francisco` as we are only interested in undergraduate institutions.

```r
univ_calif_final<- colleges %>%
  filter_all(any_vars(str_detect(.,pattern = "University of California")))
univ_calif_final
```

```
## # A tibble: 10 x 10
##    instnm      city  stabbr zip   adm_rate sat_avg pcip26 costt4_a c150_4_pooled
##    <chr>       <chr> <chr>  <chr>    <dbl>   <dbl>  <dbl>    <dbl>         <dbl>
##  1 University~ La J~ CA     92093    0.357    1324  0.216    31043         0.872
##  2 University~ Irvi~ CA     92697    0.406    1206  0.107    31198         0.876
##  3 University~ Rive~ CA     92521    0.663    1078  0.149    31494         0.73 
##  4 University~ Los ~ CA     9009~    0.180    1334  0.155    33078         0.911
##  5 University~ Davis CA     9561~    0.423    1218  0.198    33904         0.850
##  6 University~ Sant~ CA     9506~    0.578    1201  0.193    34608         0.776
##  7 University~ Berk~ CA     94720    0.169    1422  0.105    34924         0.916
##  8 University~ Sant~ CA     93106    0.358    1281  0.108    34998         0.816
##  9 University~ San ~ CA     9410~   NA          NA NA           NA        NA    
## 10 University~ San ~ CA     9414~   NA          NA NA           NA        NA    
## # ... with 1 more variable: pftftug1_ef <dbl>
```
"str_detect" is finding something in a string, goes through all the variables and finds a phrase/number. Very useful!!! . means check every cell
Remove `Hastings College of Law` and `UC San Francisco` and store the final data frame as a new object `univ_calif_final`.

```r
#univ_calif_final %>%
  #filter(str_detect(instnm!="San Francisco"))
```

Use `separate()` to separate institution name into two new columns "UNIV" and "CAMPUS".


9. The column `ADM_RATE` is the admissions rate by campus. Which UC has the lowest and highest admissions rates? Produce a numerical summary and an appropriate plot.




10. If you wanted to get a degree in biological or biomedical sciences, which campus confers the majority of these degrees? Produce a numerical summary and an appropriate plot.




## Knit Your Output and Post to [GitHub](https://github.com/FRS417-DataScienceBiologists)
