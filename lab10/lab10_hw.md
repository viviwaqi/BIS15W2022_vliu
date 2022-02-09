---
title: "Lab 10 Homework"
author: "Victoria Liu"
date: "2022-02-08"
output:
  html_document:
    theme: spacelab
    toc: no
    keep_md: yes
---



## Instructions
Answer the following questions and complete the exercises in RMarkdown. Please embed all of your code and push your final work to your repository. Your final lab report should be organized, clean, and run free from errors. Remember, you must remove the `#` for the included code chunks to run. Be sure to add your name to the author header above. For any included plots, make sure they are clearly labeled. You are free to use any plot type that you feel best communicates the results of your analysis.  

Make sure to use the formatting conventions of RMarkdown to make your report neat and clean!  

## Load the libraries

```r
library(tidyverse)
library(janitor)
library(here)
library(naniar)
```

## Desert Ecology
For this assignment, we are going to use a modified data set on [desert ecology](http://esapubs.org/archive/ecol/E090/118/). The data are from: S. K. Morgan Ernest, Thomas J. Valone, and James H. Brown. 2009. Long-term monitoring and experimental manipulation of a Chihuahuan Desert ecosystem near Portal, Arizona, USA. Ecology 90:1708.

```r
deserts <- read_csv(here("lab10", "data", "surveys_complete.csv"))
```

```
## Rows: 34786 Columns: 13
## -- Column specification --------------------------------------------------------
## Delimiter: ","
## chr (6): species_id, sex, genus, species, taxa, plot_type
## dbl (7): record_id, month, day, year, plot_id, hindfoot_length, weight
## 
## i Use `spec()` to retrieve the full column specification for this data.
## i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

1. Use the function(s) of your choice to get an idea of its structure, including how NA's are treated. Are the data tidy?  

```r
glimpse(deserts)
```

```
## Rows: 34,786
## Columns: 13
## $ record_id       <dbl> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16,~
## $ month           <dbl> 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, ~
## $ day             <dbl> 16, 16, 16, 16, 16, 16, 16, 16, 16, 16, 16, 16, 16, 16~
## $ year            <dbl> 1977, 1977, 1977, 1977, 1977, 1977, 1977, 1977, 1977, ~
## $ plot_id         <dbl> 2, 3, 2, 7, 3, 1, 2, 1, 1, 6, 5, 7, 3, 8, 6, 4, 3, 2, ~
## $ species_id      <chr> "NL", "NL", "DM", "DM", "DM", "PF", "PE", "DM", "DM", ~
## $ sex             <chr> "M", "M", "F", "M", "M", "M", "F", "M", "F", "F", "F",~
## $ hindfoot_length <dbl> 32, 33, 37, 36, 35, 14, NA, 37, 34, 20, 53, 38, 35, NA~
## $ weight          <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA~
## $ genus           <chr> "Neotoma", "Neotoma", "Dipodomys", "Dipodomys", "Dipod~
## $ species         <chr> "albigula", "albigula", "merriami", "merriami", "merri~
## $ taxa            <chr> "Rodent", "Rodent", "Rodent", "Rodent", "Rodent", "Rod~
## $ plot_type       <chr> "Control", "Long-term Krat Exclosure", "Control", "Rod~
```
The data is tidy

2. How many genera and species are represented in the data? What are the total number of observations? Which species is most/ least frequently sampled in the study?

```r
deserts %>%
  summarize(n_genera=n_distinct(genus),
            n_species=n_distinct(species),
            n=n())
```

```
## # A tibble: 1 x 3
##   n_genera n_species     n
##      <int>     <int> <int>
## 1       26        40 34786
```


```r
deserts %>%
  count(genus, species) %>%
  arrange(desc(n))
```

```
## # A tibble: 48 x 3
##    genus           species          n
##    <chr>           <chr>        <int>
##  1 Dipodomys       merriami     10596
##  2 Chaetodipus     penicillatus  3123
##  3 Dipodomys       ordii         3027
##  4 Chaetodipus     baileyi       2891
##  5 Reithrodontomys megalotis     2609
##  6 Dipodomys       spectabilis   2504
##  7 Onychomys       torridus      2249
##  8 Perognathus     flavus        1597
##  9 Peromyscus      eremicus      1299
## 10 Neotoma         albigula      1252
## # ... with 38 more rows
```

3. What is the proportion of taxa included in this study? Show a table and plot that reflects this count.

```r
deserts %>%
  tabyl(taxa)
```

```
##     taxa     n      percent
##     Bird   450 0.0129362387
##   Rabbit    75 0.0021560398
##  Reptile    14 0.0004024608
##   Rodent 34247 0.9845052607
```

```r
deserts %>% 
  ggplot(aes(x = taxa, fill = taxa)) +
  geom_bar() +
  scale_y_log10() +
  labs(title = "Total Members in Each Taxon",
       x = "Taxonomic Group",
       y= "Number of Memebers (in log10)")
```

![](lab10_hw_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

4. For the taxa included in the study, use the fill option to show the proportion of individuals sampled by `plot_type.`

```r
deserts %>% 
  ggplot(aes(x = taxa, fill = plot_type)) +
  geom_bar(position = "dodge") +
  scale_y_log10() +
  labs(title = "Total Members in Each Taxon",
       x = "Taxonomic Group",
       y= "Number of Memebers (in log10)")
```

![](lab10_hw_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

5. What is the range of weight for each species included in the study? Remove any observations of weight that are NA so they do not show up in the plot.

```r
deserts %>% 
  filter(weight!="NA") %>%
  ggplot(aes(x = species, y = weight)) +
  geom_boxplot() + 
  scale_y_log10() +
  coord_flip() +
  theme(axis.text.x = element_text(angle = 60, hjust = 1)) +
  labs(title = "Weight of Different Species",
       x = "Species Name",
       y= "Weight (in log10)")
```

![](lab10_hw_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

6. Add another layer to your answer from #5 using `geom_point` to get an idea of how many measurements were taken for each species.

```r
deserts %>% 
  filter(weight!="NA") %>%
  ggplot(aes(x = species, y = weight)) +
  geom_boxplot() + 
  geom_point(alpha=0.2, position = "jitter") +
  scale_y_log10() +
  coord_flip() +
  theme(axis.text.x = element_text(angle = 60, hjust = 1)) +
  labs(title = "Weight of Different Species",
       x = "Species Name",
       y= "Weight (in log10)")
```

![](lab10_hw_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

7. [Dipodomys merriami](https://en.wikipedia.org/wiki/Merriam's_kangaroo_rat) is the most frequently sampled animal in the study. How have the number of observations of this species changed over the years included in the study?

```r
deserts %>%
  filter(species == "merriami") %>%
  group_by(year) %>%
  summarise(species, year, n=n())
```

```
## `summarise()` has grouped output by 'year'. You can override using the `.groups`
## argument.
```

```
## # A tibble: 10,596 x 3
## # Groups:   year [26]
##     year species      n
##    <dbl> <chr>    <int>
##  1  1977 merriami   264
##  2  1977 merriami   264
##  3  1977 merriami   264
##  4  1977 merriami   264
##  5  1977 merriami   264
##  6  1977 merriami   264
##  7  1977 merriami   264
##  8  1977 merriami   264
##  9  1977 merriami   264
## 10  1977 merriami   264
## # ... with 10,586 more rows
```


```r
deserts %>%
  filter(species == "merriami") %>%
  ggplot(aes(x=as.factor(year), fill=year)) +
  geom_bar()+
  theme(axis.text.x = element_text(angle = 60, hjust = 1)) +
  labs(title = "D.Merriami Counts Over Time",
       x = "Year",
       y= "Counts")
```

![](lab10_hw_files/figure-html/unnamed-chunk-12-1.png)<!-- -->

8. What is the relationship between `weight` and `hindfoot` length? Consider whether or not over plotting is an issue.

```r
deserts %>%
  ggplot(aes(x=hindfoot_length, y=weight, color=species))+
  geom_point(position = "jitter")
```

```
## Warning: Removed 4048 rows containing missing values (geom_point).
```

![](lab10_hw_files/figure-html/unnamed-chunk-13-1.png)<!-- -->

9. Which two species have, on average, the highest weight? Once you have identified them, make a new column that is a ratio of `weight` to `hindfoot_length`. Make a plot that shows the range of this new ratio and fill by sex.

```r
deserts %>%
  filter(weight!="NA") %>% 
  group_by(species) %>%
  summarise(mean_weight=mean(weight)) %>%
  arrange(desc(mean_weight))
```

```
## # A tibble: 22 x 2
##    species      mean_weight
##    <chr>              <dbl>
##  1 albigula           159. 
##  2 spectabilis        120. 
##  3 spilosoma           93.5
##  4 hispidus            65.6
##  5 fulviventer         58.9
##  6 ochrognathus        55.4
##  7 ordii               48.9
##  8 merriami            43.2
##  9 baileyi             31.7
## 10 leucogaster         31.6
## # ... with 12 more rows
```

```r
heavy_spec_ratio<- deserts %>%
  filter(species == "albigula" | species == "spectabilis") %>% 
  filter(weight!="NA" & hindfoot_length!="NA") %>% 
  mutate(ratio=weight/hindfoot_length) 
heavy_spec_ratio
```

```
## # A tibble: 3,072 x 14
##    record_id month   day  year plot_id species_id sex   hindfoot_length weight
##        <dbl> <dbl> <dbl> <dbl>   <dbl> <chr>      <chr>           <dbl>  <dbl>
##  1       357    11    12  1977       9 DS         F                  50    117
##  2       362    11    12  1977       1 DS         F                  51    121
##  3       367    11    12  1977      20 DS         M                  51    115
##  4       377    11    12  1977       9 DS         F                  48    120
##  5       381    11    13  1977      17 DS         F                  48    118
##  6       383    11    13  1977      11 DS         F                  52    126
##  7       385    11    13  1977      17 DS         M                  50    132
##  8       392    11    13  1977      11 DS         F                  53    122
##  9       394    11    13  1977       4 DS         F                  48    107
## 10       398    11    13  1977       4 DS         F                  50    115
## # ... with 3,062 more rows, and 5 more variables: genus <chr>, species <chr>,
## #   taxa <chr>, plot_type <chr>, ratio <dbl>
```

```r
heavy_spec_ratio %>%
  filter(sex!="NA") %>%
  ggplot(aes(x=species, y=ratio, fill=sex))+
  geom_boxplot()
```

![](lab10_hw_files/figure-html/unnamed-chunk-16-1.png)<!-- -->

10. Make one plot of your choice! Make sure to include at least two of the aesthetics options you have learned.

```r
deserts %>% 
  ggplot(aes(x = genus, fill = sex)) +
  geom_bar(position = "dodge") +
  scale_y_log10() +
  coord_flip()+
  labs(title = "Total Members In Each Genus By Sex",
       x = "Genus",
       y= "Number of Members (in log10)")
```

![](lab10_hw_files/figure-html/unnamed-chunk-17-1.png)<!-- -->

## Push your final code to GitHub!
Please be sure that you check the `keep md` file in the knit preferences. 
