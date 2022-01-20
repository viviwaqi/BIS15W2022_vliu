---
title: "Lab 6 Homework"
author: "Joel Ledford"
date: "2022-01-20"
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
library(skimr)
```

For this assignment we are going to work with a large data set from the [United Nations Food and Agriculture Organization](http://www.fao.org/about/en/) on world fisheries. These data are pretty wild, so we need to do some cleaning. First, load the data.  

Load the data `FAO_1950to2012_111914.csv` as a new object titled `fisheries`.

```r
fisheries <- readr::read_csv(file = "data/FAO_1950to2012_111914.csv")
```

```
## Rows: 17692 Columns: 71
```

```
## -- Column specification --------------------------------------------------------
## Delimiter: ","
## chr (69): Country, Common name, ISSCAAP taxonomic group, ASFIS species#, ASF...
## dbl  (2): ISSCAAP group#, FAO major fishing area
```

```
## 
## i Use `spec()` to retrieve the full column specification for this data.
## i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

1. Do an exploratory analysis of the data (your choice). What are the names of the variables, what are the dimensions, are there any NA's, what are the classes of the variables?  

```r
glimpse(fisheries)
```

```
## Rows: 17,692
## Columns: 71
## $ Country                   <chr> "Albania", "Albania", "Albania", "Albania", ~
## $ `Common name`             <chr> "Angelsharks, sand devils nei", "Atlantic bo~
## $ `ISSCAAP group#`          <dbl> 38, 36, 37, 45, 32, 37, 33, 45, 38, 57, 33, ~
## $ `ISSCAAP taxonomic group` <chr> "Sharks, rays, chimaeras", "Tunas, bonitos, ~
## $ `ASFIS species#`          <chr> "10903XXXXX", "1750100101", "17710001XX", "2~
## $ `ASFIS species name`      <chr> "Squatinidae", "Sarda sarda", "Sphyraena spp~
## $ `FAO major fishing area`  <dbl> 37, 37, 37, 37, 37, 37, 37, 37, 37, 37, 37, ~
## $ Measure                   <chr> "Quantity (tonnes)", "Quantity (tonnes)", "Q~
## $ `1950`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1951`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1952`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1953`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1954`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1955`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1956`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1957`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1958`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1959`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1960`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1961`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1962`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1963`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1964`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1965`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1966`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1967`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1968`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1969`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1970`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1971`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1972`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1973`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1974`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1975`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1976`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1977`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1978`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1979`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1980`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1981`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1982`                    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, ~
## $ `1983`                    <chr> NA, NA, NA, NA, NA, NA, "559", NA, NA, NA, N~
## $ `1984`                    <chr> NA, NA, NA, NA, NA, NA, "392", NA, NA, NA, N~
## $ `1985`                    <chr> NA, NA, NA, NA, NA, NA, "406", NA, NA, NA, N~
## $ `1986`                    <chr> NA, NA, NA, NA, NA, NA, "499", NA, NA, NA, N~
## $ `1987`                    <chr> NA, NA, NA, NA, NA, NA, "564", NA, NA, NA, N~
## $ `1988`                    <chr> NA, NA, NA, NA, NA, NA, "724", NA, NA, NA, N~
## $ `1989`                    <chr> NA, NA, NA, NA, NA, NA, "583", NA, NA, NA, N~
## $ `1990`                    <chr> NA, NA, NA, NA, NA, NA, "754", NA, NA, NA, N~
## $ `1991`                    <chr> NA, NA, NA, NA, NA, NA, "283", NA, NA, NA, N~
## $ `1992`                    <chr> NA, NA, NA, NA, NA, NA, "196", NA, NA, NA, N~
## $ `1993`                    <chr> NA, NA, NA, NA, NA, NA, "150 F", NA, NA, NA,~
## $ `1994`                    <chr> NA, NA, NA, NA, NA, NA, "100 F", NA, NA, NA,~
## $ `1995`                    <chr> "0 0", "1", NA, "0 0", "0 0", NA, "52", "30"~
## $ `1996`                    <chr> "53", "2", NA, "3", "2", NA, "104", "8", NA,~
## $ `1997`                    <chr> "20", "0 0", NA, "0 0", "0 0", NA, "65", "4"~
## $ `1998`                    <chr> "31", "12", NA, NA, NA, NA, "220", "18", NA,~
## $ `1999`                    <chr> "30", "30", NA, NA, NA, NA, "220", "18", NA,~
## $ `2000`                    <chr> "30", "25", "2", NA, NA, NA, "220", "20", NA~
## $ `2001`                    <chr> "16", "30", NA, NA, NA, NA, "120", "23", NA,~
## $ `2002`                    <chr> "79", "24", NA, "34", "6", NA, "150", "84", ~
## $ `2003`                    <chr> "1", "4", NA, "22", NA, NA, "84", "178", NA,~
## $ `2004`                    <chr> "4", "2", "2", "15", "1", "2", "76", "285", ~
## $ `2005`                    <chr> "68", "23", "4", "12", "5", "6", "68", "150"~
## $ `2006`                    <chr> "55", "30", "7", "18", "8", "9", "86", "102"~
## $ `2007`                    <chr> "12", "19", NA, NA, NA, NA, "132", "18", NA,~
## $ `2008`                    <chr> "23", "27", NA, NA, NA, NA, "132", "23", NA,~
## $ `2009`                    <chr> "14", "21", NA, NA, NA, NA, "154", "20", NA,~
## $ `2010`                    <chr> "78", "23", "7", NA, NA, NA, "80", "228", NA~
## $ `2011`                    <chr> "12", "12", NA, NA, NA, NA, "88", "9", NA, "~
## $ `2012`                    <chr> "5", "5", NA, NA, NA, NA, "129", "290", NA, ~
```

2. Use `janitor` to rename the columns and make them easier to use. As part of this cleaning step, change `country`, `isscaap_group_number`, `asfis_species_number`, and `fao_major_fishing_area` to data class factor. 

```r
library("janitor")
fisheries<- clean_names(fisheries)
names(fisheries)
```

```
##  [1] "country"                 "common_name"            
##  [3] "isscaap_group_number"    "isscaap_taxonomic_group"
##  [5] "asfis_species_number"    "asfis_species_name"     
##  [7] "fao_major_fishing_area"  "measure"                
##  [9] "x1950"                   "x1951"                  
## [11] "x1952"                   "x1953"                  
## [13] "x1954"                   "x1955"                  
## [15] "x1956"                   "x1957"                  
## [17] "x1958"                   "x1959"                  
## [19] "x1960"                   "x1961"                  
## [21] "x1962"                   "x1963"                  
## [23] "x1964"                   "x1965"                  
## [25] "x1966"                   "x1967"                  
## [27] "x1968"                   "x1969"                  
## [29] "x1970"                   "x1971"                  
## [31] "x1972"                   "x1973"                  
## [33] "x1974"                   "x1975"                  
## [35] "x1976"                   "x1977"                  
## [37] "x1978"                   "x1979"                  
## [39] "x1980"                   "x1981"                  
## [41] "x1982"                   "x1983"                  
## [43] "x1984"                   "x1985"                  
## [45] "x1986"                   "x1987"                  
## [47] "x1988"                   "x1989"                  
## [49] "x1990"                   "x1991"                  
## [51] "x1992"                   "x1993"                  
## [53] "x1994"                   "x1995"                  
## [55] "x1996"                   "x1997"                  
## [57] "x1998"                   "x1999"                  
## [59] "x2000"                   "x2001"                  
## [61] "x2002"                   "x2003"                  
## [63] "x2004"                   "x2005"                  
## [65] "x2006"                   "x2007"                  
## [67] "x2008"                   "x2009"                  
## [69] "x2010"                   "x2011"                  
## [71] "x2012"
```


```r
fisheries$country <-as.factor(fisheries$country)
fisheries$isscaap_group_number <-as.factor(fisheries$isscaap_group_number)
fisheries$asif_species_number <-as.factor(fisheries$asfis_species_number)
fisheries$fao_major_fishing_area <-as.factor(fisheries$fao_major_fishing_area)
  class(fisheries$fao_major_fishing_area)
```

```
## [1] "factor"
```



We need to deal with the years because they are being treated as characters and start with an X. We also have the problem that the column names that are years actually represent data. We haven't discussed tidy data yet, so here is some help. You should run this ugly chunk to transform the data for the rest of the homework. It will only work if you have used janitor to rename the variables in question 2!

```r
fisheries_tidy <- fisheries %>% 
  pivot_longer(-c(country,common_name,isscaap_group_number,isscaap_taxonomic_group,asfis_species_number,asfis_species_name,fao_major_fishing_area,measure),
               names_to = "year",
               values_to = "catch",
               values_drop_na = TRUE) %>% 
  mutate(year= as.numeric(str_replace(year, 'x', ''))) %>% 
  mutate(catch= str_replace(catch, c(' F'), replacement = '')) %>% 
  mutate(catch= str_replace(catch, c('...'), replacement = '')) %>% 
  mutate(catch= str_replace(catch, c('-'), replacement = '')) %>% 
  mutate(catch= str_replace(catch, c('0 0'), replacement = ''))
```

```
## Warning in mask$eval_all_mutate(quo): NAs introduced by coercion
```

```r
fisheries_tidy$catch <- as.numeric(fisheries_tidy$catch)
```

```
## Warning: NAs introduced by coercion
```

3. How many countries are represented in the data? Provide a count and list their names.

```r
fisheries_tidy
```

```
## # A tibble: 394,463 x 10
##    country common_name     isscaap_group_nu~ isscaap_taxonomic~ asfis_species_n~
##    <fct>   <chr>           <fct>             <chr>              <chr>           
##  1 Albania Angelsharks, s~ 38                Sharks, rays, chi~ 10903XXXXX      
##  2 Albania Angelsharks, s~ 38                Sharks, rays, chi~ 10903XXXXX      
##  3 Albania Angelsharks, s~ 38                Sharks, rays, chi~ 10903XXXXX      
##  4 Albania Angelsharks, s~ 38                Sharks, rays, chi~ 10903XXXXX      
##  5 Albania Angelsharks, s~ 38                Sharks, rays, chi~ 10903XXXXX      
##  6 Albania Angelsharks, s~ 38                Sharks, rays, chi~ 10903XXXXX      
##  7 Albania Angelsharks, s~ 38                Sharks, rays, chi~ 10903XXXXX      
##  8 Albania Angelsharks, s~ 38                Sharks, rays, chi~ 10903XXXXX      
##  9 Albania Angelsharks, s~ 38                Sharks, rays, chi~ 10903XXXXX      
## 10 Albania Angelsharks, s~ 38                Sharks, rays, chi~ 10903XXXXX      
## # ... with 394,453 more rows, and 5 more variables: asfis_species_name <chr>,
## #   fao_major_fishing_area <fct>, measure <chr>, year <dbl>, catch <dbl>
```

4. Refocus the data only to include country, isscaap_taxonomic_group, asfis_species_name, asfis_species_number, year, catch.

```r
fisheries_focus<- select(fisheries_tidy, country, isscaap_taxonomic_group, asfis_species_name, asfis_species_number, year, catch)
fisheries_focus
```

```
## # A tibble: 394,463 x 6
##    country isscaap_taxonomic_group asfis_species_n~ asfis_species_n~  year catch
##    <fct>   <chr>                   <chr>            <chr>            <dbl> <dbl>
##  1 Albania Sharks, rays, chimaeras Squatinidae      10903XXXXX        1995    NA
##  2 Albania Sharks, rays, chimaeras Squatinidae      10903XXXXX        1996    53
##  3 Albania Sharks, rays, chimaeras Squatinidae      10903XXXXX        1997    20
##  4 Albania Sharks, rays, chimaeras Squatinidae      10903XXXXX        1998    31
##  5 Albania Sharks, rays, chimaeras Squatinidae      10903XXXXX        1999    30
##  6 Albania Sharks, rays, chimaeras Squatinidae      10903XXXXX        2000    30
##  7 Albania Sharks, rays, chimaeras Squatinidae      10903XXXXX        2001    16
##  8 Albania Sharks, rays, chimaeras Squatinidae      10903XXXXX        2002    79
##  9 Albania Sharks, rays, chimaeras Squatinidae      10903XXXXX        2003     1
## 10 Albania Sharks, rays, chimaeras Squatinidae      10903XXXXX        2004     4
## # ... with 394,453 more rows
```

5. Based on the asfis_species_number, how many distinct fish species were caught as part of these data?

```r
n_distinct(fisheries_focus$asfis_species_number)
```

```
## [1] 1553
```

6. Which country had the largest overall catch in the year 2000?


```r
fisheries_tidy %>%
  select(country, year, catch) %>% 
  filter(year == "2000") 
```

```
## # A tibble: 8,793 x 3
##    country  year catch
##    <fct>   <dbl> <dbl>
##  1 Albania  2000    30
##  2 Albania  2000    25
##  3 Albania  2000     2
##  4 Albania  2000    NA
##  5 Albania  2000    20
##  6 Albania  2000    50
##  7 Albania  2000    85
##  8 Albania  2000    41
##  9 Albania  2000    90
## 10 Albania  2000    45
## # ... with 8,783 more rows
```

```r
#which.max(catch)
```

7. Which country caught the most sardines (_Sardina pilchardus_) between the years 1990-2000?

```r
fisheries_tidy %>%
  select(country, asfis_species_name, catch, year) %>%
  filter(asfis_species_name == "Sardina pilchardus", between(year, 1990, 2000))
```

```
## # A tibble: 336 x 4
##    country asfis_species_name catch  year
##    <fct>   <chr>              <dbl> <dbl>
##  1 Albania Sardina pilchardus     8  1990
##  2 Albania Sardina pilchardus    NA  1991
##  3 Albania Sardina pilchardus    34  1992
##  4 Albania Sardina pilchardus    50  1993
##  5 Albania Sardina pilchardus    NA  1994
##  6 Albania Sardina pilchardus    NA  1995
##  7 Albania Sardina pilchardus    NA  1996
##  8 Albania Sardina pilchardus    28  1997
##  9 Albania Sardina pilchardus    28  1998
## 10 Albania Sardina pilchardus    40  1999
## # ... with 326 more rows
```

```r
  #arrange(desc(catch))
```

8. Which five countries caught the most cephalopods between 2008-2012?

```r
fisheries_tidy %>%
  select(country, isscaap_taxonomic_group, year, catch) %>%
  filter(isscaap_taxonomic_group == "Squids, cuttlefishes, octopuses", between(year, 2008, 2012)) %>%
  arrange(desc(catch))
```

```
## # A tibble: 1,801 x 4
##    country                  isscaap_taxonomic_group          year catch
##    <fct>                    <chr>                           <dbl> <dbl>
##  1 Indonesia                Squids, cuttlefishes, octopuses  2012   991
##  2 China                    Squids, cuttlefishes, octopuses  2008   981
##  3 Chile                    Squids, cuttlefishes, octopuses  2012   965
##  4 United States of America Squids, cuttlefishes, octopuses  2010   901
##  5 China                    Squids, cuttlefishes, octopuses  2012   845
##  6 Japan                    Squids, cuttlefishes, octopuses  2010   832
##  7 China                    Squids, cuttlefishes, octopuses  2010   826
##  8 Peru                     Squids, cuttlefishes, octopuses  2010   822
##  9 Korea, Republic of       Squids, cuttlefishes, octopuses  2008   816
## 10 Peru                     Squids, cuttlefishes, octopuses  2009   805
## # ... with 1,791 more rows
```

```r
#The top 5 are Indonesia, China, Chile, USA and Japan
```

9. Which species had the highest catch total between 2008-2012? (hint: Osteichthyes is not a species)

```r
fisheries_tidy %>%
  select(asfis_species_name, year, catch) %>%
  filter(between(year, 2008, 2012)) 
```

```
## # A tibble: 51,014 x 3
##    asfis_species_name  year catch
##    <chr>              <dbl> <dbl>
##  1 Squatinidae         2008    23
##  2 Squatinidae         2009    14
##  3 Squatinidae         2010    78
##  4 Squatinidae         2011    12
##  5 Squatinidae         2012     5
##  6 Sarda sarda         2008    27
##  7 Sarda sarda         2009    21
##  8 Sarda sarda         2010    23
##  9 Sarda sarda         2011    12
## 10 Sarda sarda         2012     5
## # ... with 51,004 more rows
```

```r
  #n_distinct(asfis_species_name) %>%
  #arrange(group_by(asfis_species_name))
```

10. Use the data to do at least one analysis of your choice.

## Push your final code to GitHub!
Please be sure that you check the `keep md` file in the knit preferences.   
