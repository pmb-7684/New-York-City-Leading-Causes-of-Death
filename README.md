NYC Leading Causes of Death
================
Paula Bailey
2022-10-14

# Background

Looking at my profile, one may believe two things about me - 1. I love
large cities like Chicago and New York 2. I’m only interested in health
data set. I haven’t been to either city since I was a child; however,
both have wealth of open source data. The city of Chicago has their
[portal](https://data.cityofchicago.org/). I just discovered New York
City’s [NYCOpenData](https://opendata.cityofnewyork.us/). This site also
has an API, so I plan to practice in the near future. As for the second
question, it’s more likely because I can find more data on the subject
of health and crime.

This data comes from the NYC OpenData website. It is on the leading
causes of death in New York City by sex and ethnicity since 2007. The
cause of death is derived from the NYC death certificate which is issued
for every death that occurs in the city”.

After I complete this project, I will be completing it again in python.
Currently, I am in school and R is the language of choice. But I
realize, it is important to know python. I’ve completed a program on the
DataCamp platform…practice…practice…

# Ask

This is not a formal project, so there is not a detailed business task
(or statement). I will use the data set to explore, complete some
summary statistics and visualizations.

I will be looking at- what was the cause of death for women in NYC? What
about the top 10 causes? Does race change the top cause for women?
Basically, is it different for Black, White, Hispanic, and Asian women?

# Prepare

This csv file is named `New_York_City_leading_Causes_of_Death`. It’s in
a long format with a shape of 1,272 x 7 where each row is a cause of
death. It is contained on the NYC OpenData platform where it is updated
annually by Department of Health and Mental Hygiene (DOHMH). It was last
updated on February 8, 2022. The data is from 2007 to 2019.

The csv has the following columns (variables):  
Year Year of Death year Leading Cause the unique cause of death
leading_cause Sex Sex of decedent sex Race Race of decedent
race/ethnicity Deaths Number of People who died due to this cause deaths
Death Rate Death rate within sex and race category death_rate Age
Adjusted Death Rate Age adjusted death rate within sex and race
age_adjusted_death_rate

Based on the information from the website all of the variables are in
plain text. So, we will need to verify the types of each column.

The data and metadata can be found
[here.](https://data.cityofnewyork.us/Health/New-York-City-Leading-Causes-of-Death/jb7j-dtam)

After downloading the file, I renamed it to preserve the original file.
During the data cleaning (process), any changes to the data will be
saved under a file named ChangeLog.doc

# Process

``` r
library(tidyverse)
```

First, we will read in the csv file.

``` r
nycCauses <- read.csv("New_York_City_Leading_Causes_of_Death_pmb.csv")
```

``` r
#nycCauses$ï..Year <- trimws(nycCauses$ï..Year, which = c("both"))
```

``` r
head(nycCauses)
```

    ##   ï..Year
    ## 1    2019
    ## 2    2019
    ## 3    2019
    ## 4    2019
    ## 5    2019
    ## 6    2019
    ##                                                                                                                       Leading.Cause
    ## 1                                                                                    Diseases of Heart (I00-I09, I11, I13, I20-I51)
    ## 2                                                                                             Malignant Neoplasms (Cancer: C00-C97)
    ## 3 Mental and Behavioral Disorders due to Accidental Poisoning and Other Psychoactive Substance Use (F11-F16, F18-F19, X40-X42, X44)
    ## 4                                                                                                       Diabetes Mellitus (E10-E14)
    ## 5                                                                                           Influenza (Flu) and Pneumonia (J09-J18)
    ## 6                                                                                         Cerebrovascular Disease (Stroke: I60-I69)
    ##    Sex Race.Ethnicity Deaths  Death.Rate Age.Adjusted.Death.Rate
    ## 1 Male       Hispanic   1603 136.8029917              176.783287
    ## 2 Male       Hispanic   1164  99.3379179             121.5817693
    ## 3 Male       Hispanic    423 36.09960418             35.70789583
    ## 4 Male       Hispanic    245  20.9087542             25.40934387
    ## 5 Male       Hispanic    182  15.5322174             19.85821025
    ## 6 Male       Hispanic    182  15.5322174             19.55837808

Next, the structure of the file.

``` r
str(nycCauses)
```

    ## 'data.frame':    1272 obs. of  7 variables:
    ##  $ ï..Year                : int  2019 2019 2019 2019 2019 2019 2019 2019 2019 2019 ...
    ##  $ Leading.Cause          : chr  "Diseases of Heart (I00-I09, I11, I13, I20-I51)" "Malignant Neoplasms (Cancer: C00-C97)" "Mental and Behavioral Disorders due to Accidental Poisoning and Other Psychoactive Substance Use (F11-F16, F18-"| __truncated__ "Diabetes Mellitus (E10-E14)" ...
    ##  $ Sex                    : chr  "Male" "Male" "Male" "Male" ...
    ##  $ Race.Ethnicity         : chr  "Hispanic" "Hispanic" "Hispanic" "Hispanic" ...
    ##  $ Deaths                 : chr  "1603" "1164" "423" "245" ...
    ##  $ Death.Rate             : chr  "136.8029917" "99.3379179" "36.09960418" "20.9087542" ...
    ##  $ Age.Adjusted.Death.Rate: chr  "176.783287" "121.5817693" "35.70789583" "25.40934387" ...

As mentioned above, everything is a character format except for year
including deaths, death.rate, and the adjusted death rate.

I think we should make the following data changes:

-   Leading Cause, Sex, and Race.Ethnicity from CHR to FACTOR
-   Leading Cause, Sex, and Race.Ethnicity from CHR to FACTOR
-   death rates and age adjusted deaths rates from CHR to DOUBLE
-   Change ï..Year to year (column name)

Rename column ï..Year

``` r
nycCauses <- nycCauses %>% rename(year=ï..Year)
```

Before changing data types, let’s look for any NAs. (Something to note -
I don’t understand when I run the code, R does not see the NA. When I
trick further down and re run this chunk of code, it does return the
correct information. There is missing information 138 Deaths, 453
Death.Rate, and 453 Age. Adjusted.Death.Rate. I will research this
problem.)

``` r
colSums(is.na(nycCauses))
```

    ##                    year           Leading.Cause                     Sex          Race.Ethnicity 
    ##                       0                       0                       0                       0 
    ##                  Deaths              Death.Rate Age.Adjusted.Death.Rate 
    ##                       0                       0                       0

``` r
#colnames(nycCauses)[colSums(is.na(nycCauses)) >0]
```

Hmm, that is interesting. I was not expecting to see NAs. so, the data
set is somewhat small. I can look it using excel. We have the following
issues: \* Descendants with “Other” as race do not have Death rate and
age adjusted death rate. I think that is the 453 above. \* The remaining
138 descendants with missing deaths are a combination of with “other
Race” and “Not Stated” as race.

Based on the above, I will create a data set descendants with missing
deaths removed. We will name that data frame as `nycCauses_Deaths`. I
will try to complete most of the analysis with data set. Recall the
original data set is 1272x7. The set is 1134 x 7. The difference is 138.

``` r
nycCauses_Deaths <- nycCauses %>% drop_na(Deaths)
```

Change Leading causes, sex, and race to factors

``` r
# update to factors
nycCauses_Deaths$Leading.Cause = factor(nycCauses_Deaths$Leading.Cause)
nycCauses_Deaths$Sex = factor(nycCauses_Deaths$Sex)
nycCauses_Deaths$Race.Ethnicity = factor(nycCauses_Deaths$Race.Ethnicity)
# update to integer and double
nycCauses_Deaths$Deaths = as.integer(nycCauses_Deaths$Deaths)
```

    ## Warning: NAs introduced by coercion

``` r
nycCauses_Deaths$Death.Rate = as.double(nycCauses_Deaths$Death.Rate)
```

    ## Warning: NAs introduced by coercion

``` r
nycCauses_Deaths$Age.Adjusted.Death.Rate = as.double(nycCauses_Deaths$Age.Adjusted.Death.Rate)
```

    ## Warning: NAs introduced by coercion

``` r
nycCauses$Age.Adjusted.Death.Rate = as.double(nycCauses$Age.Adjusted.Death.Rate)
```

    ## Warning: NAs introduced by coercion

``` r
nycCauses$Deaths = as.integer(nycCauses$Deaths)
```

    ## Warning: NAs introduced by coercion

``` r
nycCauses$Death.Rate = as.double(nycCauses$Death.Rate)
```

    ## Warning: NAs introduced by coercion

We will also create a second data set names`nycCause_noNA`, where we
remove all NA. This will leave us with a 819x7 data set.

``` r
nycCauses_noNA   <- nycCauses_Deaths %>% drop_na(Death.Rate, Age.Adjusted.Death.Rate)
```

Let’s confirm our changes

``` r
str(nycCauses_Deaths)
```

    ## 'data.frame':    1272 obs. of  7 variables:
    ##  $ year                   : int  2019 2019 2019 2019 2019 2019 2019 2019 2019 2019 ...
    ##  $ Leading.Cause          : Factor w/ 34 levels "Accidents Except Drug Poisoning (V01-X39, X43, X45-X59, Y85-Y86)",..: 19 26 27 18 22 10 1 13 15 20 ...
    ##  $ Sex                    : Factor w/ 4 levels "F","Female","M",..: 4 4 4 4 4 4 4 4 4 4 ...
    ##  $ Race.Ethnicity         : Factor w/ 8 levels "Asian and Pacific Islander",..: 3 3 3 3 3 3 3 3 3 3 ...
    ##  $ Deaths                 : int  1603 1164 423 245 182 182 174 171 170 135 ...
    ##  $ Death.Rate             : num  136.8 99.3 36.1 20.9 15.5 ...
    ##  $ Age.Adjusted.Death.Rate: num  176.8 121.6 35.7 25.4 19.9 ...

``` r
nycCauses_Deaths %>% select(Deaths, Death.Rate, Age.Adjusted.Death.Rate) %>% summary()
```

    ##      Deaths         Death.Rate     Age.Adjusted.Death.Rate
    ##  Min.   :   1.0   Min.   :  2.40   Min.   :  2.50         
    ##  1st Qu.:  29.0   1st Qu.: 11.95   1st Qu.: 12.00         
    ##  Median : 136.5   Median : 18.50   Median : 20.00         
    ##  Mean   : 422.9   Mean   : 53.52   Mean   : 53.21         
    ##  3rd Qu.: 291.5   3rd Qu.: 66.07   3rd Qu.: 77.90         
    ##  Max.   :7050.0   Max.   :491.40   Max.   :414.59         
    ##  NA's   :138      NA's   :453      NA's   :453

``` r
#nycCauses  %>% summary()
```

``` r
nycCauses_noNA %>% select(Deaths, Death.Rate, Age.Adjusted.Death.Rate) %>% summary()
```

    ##      Deaths         Death.Rate     Age.Adjusted.Death.Rate
    ##  Min.   :   5.0   Min.   :  2.40   Min.   :  2.50         
    ##  1st Qu.: 102.0   1st Qu.: 11.95   1st Qu.: 12.00         
    ##  Median : 207.0   Median : 18.50   Median : 20.00         
    ##  Mean   : 577.3   Mean   : 53.52   Mean   : 53.21         
    ##  3rd Qu.: 472.5   3rd Qu.: 66.07   3rd Qu.: 77.90         
    ##  Max.   :7050.0   Max.   :491.40   Max.   :414.59

Mow our data sets seems to be clean. As mentioned above we will use
nycCauses_Deaths and nycCauses_noNA.

# Analyze

``` r
nycCauses_Deaths %>% 
  group_by(year, Leading.Cause, Sex) %>% 
  summarise(mean = mean(Deaths)) %>%
  filter(year == 2019 & Sex == "Female" & grepl('Heart|Diabetes', Leading.Cause))
```

    ## `summarise()` has grouped output by 'year', 'Leading.Cause'. You can override using the `.groups` argument.

    ## # A tibble: 2 x 4
    ## # Groups:   year, Leading.Cause [2]
    ##    year Leading.Cause                                  Sex     mean
    ##   <int> <fct>                                          <fct>  <dbl>
    ## 1  2019 Diabetes Mellitus (E10-E14)                    Female  129.
    ## 2  2019 Diseases of Heart (I00-I09, I11, I13, I20-I51) Female 1266.

We can determine top 10 deaths for female in 2019. The number one cause
of death was Diseases of Heart (I00-I09, I11, I13, I20-I51) an average
of 1266 women dying. A distance second was Malignant Neoplasms (Cancer:
C00-C97) with 898 cases.

``` r
nycCauses_Deaths %>% 
      group_by(year, Leading.Cause, Sex) %>% 
      summarise(average = mean(Deaths)) %>%
      filter(year == 2019 & Sex == "Female") %>%
      arrange(desc(average)) %>%
      select(Leading.Cause, average) %>%
      head(10)
```

    ## `summarise()` has grouped output by 'year', 'Leading.Cause'. You can override using the `.groups` argument.
    ## Adding missing grouping variables: `year`

    ## # A tibble: 10 x 3
    ## # Groups:   year, Leading.Cause [10]
    ##     year Leading.Cause                                                                                average
    ##    <int> <fct>                                                                                          <dbl>
    ##  1  2019 Diseases of Heart (I00-I09, I11, I13, I20-I51)                                                1266. 
    ##  2  2019 Malignant Neoplasms (Cancer: C00-C97)                                                          898. 
    ##  3  2019 All Other Causes                                                                               804. 
    ##  4  2019 Chronic Lower Respiratory Diseases (J40-J47)                                                   159  
    ##  5  2019 Cerebrovascular Disease (Stroke: I60-I69)                                                      155. 
    ##  6  2019 Alzheimer's Disease (G30)                                                                      133. 
    ##  7  2019 Diabetes Mellitus (E10-E14)                                                                    129. 
    ##  8  2019 Influenza (Flu) and Pneumonia (J09-J18)                                                        110. 
    ##  9  2019 Essential Hypertension and Renal Diseases (I10, I12)                                           107. 
    ## 10  2019 Mental and Behavioral Disorders due to Accidental Poisoning and Other Psychoactive Substanc~    59.7

We will analyze similar results for women by race.

``` r
nycCauses_Deaths %>% 
      group_by(year, Race.Ethnicity,Leading.Cause) %>% 
      filter(year == 2019 & Sex == "Female" & Race.Ethnicity == 'Non-Hispanic Black') %>%
      summarise(average = mean(Deaths)) %>%
      arrange(desc(average)) %>%
      select(Race.Ethnicity, Leading.Cause, average) 
```

    ## `summarise()` has grouped output by 'year', 'Race.Ethnicity'. You can override using the `.groups` argument.
    ## Adding missing grouping variables: `year`

    ## # A tibble: 11 x 4
    ## # Groups:   year, Race.Ethnicity [1]
    ##     year Race.Ethnicity     Leading.Cause                                                             average
    ##    <int> <fct>              <fct>                                                                       <dbl>
    ##  1  2019 Non-Hispanic Black Diseases of Heart (I00-I09, I11, I13, I20-I51)                               2483
    ##  2  2019 Non-Hispanic Black Malignant Neoplasms (Cancer: C00-C97)                                        1753
    ##  3  2019 Non-Hispanic Black All Other Causes                                                             1578
    ##  4  2019 Non-Hispanic Black Diabetes Mellitus (E10-E14)                                                   383
    ##  5  2019 Non-Hispanic Black Cerebrovascular Disease (Stroke: I60-I69)                                     316
    ##  6  2019 Non-Hispanic Black Essential Hypertension and Renal Diseases (I10, I12)                          290
    ##  7  2019 Non-Hispanic Black Chronic Lower Respiratory Diseases (J40-J47)                                  242
    ##  8  2019 Non-Hispanic Black Influenza (Flu) and Pneumonia (J09-J18)                                       227
    ##  9  2019 Non-Hispanic Black Alzheimer's Disease (G30)                                                     171
    ## 10  2019 Non-Hispanic Black Mental and Behavioral Disorders due to Accidental Poisoning and Other Ps~     120
    ## 11  2019 Non-Hispanic Black Nephritis, Nephrotic Syndrome and Nephrisis (N00-N07, N17-N19, N25-N27)        96

For Black, the number one cause is Diseases of Heart with average 2483
deaths. This is followed by Cancer and Diabetes. Since I don’t know what
causes are in All Other Causes, I’m going to skip it when looking at all
races.

``` r
nycCauses_Deaths %>% 
      group_by(year, Race.Ethnicity,Leading.Cause) %>% 
      filter(year == 2019 & Sex == "Female" & Race.Ethnicity == 'Non-Hispanic White') %>%
      summarise(average = mean(Deaths)) %>%
      arrange(desc(average)) %>%
      select(Race.Ethnicity, Leading.Cause, average) 
```

    ## `summarise()` has grouped output by 'year', 'Race.Ethnicity'. You can override using the `.groups` argument.
    ## Adding missing grouping variables: `year`

    ## # A tibble: 11 x 4
    ## # Groups:   year, Race.Ethnicity [1]
    ##     year Race.Ethnicity     Leading.Cause                                                             average
    ##    <int> <fct>              <fct>                                                                       <dbl>
    ##  1  2019 Non-Hispanic White Diseases of Heart (I00-I09, I11, I13, I20-I51)                               4001
    ##  2  2019 Non-Hispanic White Malignant Neoplasms (Cancer: C00-C97)                                        2655
    ##  3  2019 Non-Hispanic White All Other Causes                                                             2264
    ##  4  2019 Non-Hispanic White Chronic Lower Respiratory Diseases (J40-J47)                                  480
    ##  5  2019 Non-Hispanic White Cerebrovascular Disease (Stroke: I60-I69)                                     390
    ##  6  2019 Non-Hispanic White Alzheimer's Disease (G30)                                                     341
    ##  7  2019 Non-Hispanic White Influenza (Flu) and Pneumonia (J09-J18)                                       308
    ##  8  2019 Non-Hispanic White Essential Hypertension and Renal Diseases (I10, I12)                          228
    ##  9  2019 Non-Hispanic White Diabetes Mellitus (E10-E14)                                                   197
    ## 10  2019 Non-Hispanic White Accidents Except Drug Poisoning (V01-X39, X43, X45-X59, Y85-Y86)              167
    ## 11  2019 Non-Hispanic White Mental and Behavioral Disorders due to Accidental Poisoning and Other Ps~     125

For Non-Hispanic White, the number one cause is Diseases of Heart with
average 4001 deaths. This is followed by Cancer and Chronic Lower
Respiratory Diseases.

``` r
nycCauses_Deaths %>% 
      group_by(year, Race.Ethnicity,Leading.Cause) %>% 
      filter(year == 2019 & Sex == "Female" & Race.Ethnicity == 'Hispanic') %>%
      summarise(average = mean(Deaths)) %>%
      arrange(desc(average)) %>%
      select(Race.Ethnicity, Leading.Cause, average) 
```

    ## `summarise()` has grouped output by 'year', 'Race.Ethnicity'. You can override using the `.groups` argument.
    ## Adding missing grouping variables: `year`

    ## # A tibble: 11 x 4
    ## # Groups:   year, Race.Ethnicity [1]
    ##     year Race.Ethnicity Leading.Cause                                                                 average
    ##    <int> <fct>          <fct>                                                                           <dbl>
    ##  1  2019 Hispanic       Diseases of Heart (I00-I09, I11, I13, I20-I51)                                   1574
    ##  2  2019 Hispanic       All Other Causes                                                                 1279
    ##  3  2019 Hispanic       Malignant Neoplasms (Cancer: C00-C97)                                            1182
    ##  4  2019 Hispanic       Cerebrovascular Disease (Stroke: I60-I69)                                         249
    ##  5  2019 Hispanic       Alzheimer's Disease (G30)                                                         231
    ##  6  2019 Hispanic       Diabetes Mellitus (E10-E14)                                                       214
    ##  7  2019 Hispanic       Chronic Lower Respiratory Diseases (J40-J47)                                      178
    ##  8  2019 Hispanic       Influenza (Flu) and Pneumonia (J09-J18)                                           156
    ##  9  2019 Hispanic       Essential Hypertension and Renal Diseases (I10, I12)                              151
    ## 10  2019 Hispanic       Mental and Behavioral Disorders due to Accidental Poisoning and Other Psycho~     104
    ## 11  2019 Hispanic       Accidents Except Drug Poisoning (V01-X39, X43, X45-X59, Y85-Y86)                   98

For Hispanic, the number one cause is Diseases of Heart with average
1574 deaths. This is followed by Cancer and Cerebrovascular Disease.

``` r
nycCauses_Deaths %>% 
      group_by(year, Race.Ethnicity,Leading.Cause) %>% 
      filter(year == 2019 & Sex == "Female" & Race.Ethnicity == 'Asian and Pacific Islander') %>%
      summarise(average = mean(Deaths)) %>%
      arrange(desc(average)) %>%
      select(Race.Ethnicity, Leading.Cause, average) 
```

    ## `summarise()` has grouped output by 'year', 'Race.Ethnicity'. You can override using the `.groups` argument.
    ## Adding missing grouping variables: `year`

    ## # A tibble: 11 x 4
    ## # Groups:   year, Race.Ethnicity [1]
    ##     year Race.Ethnicity             Leading.Cause                                                    average
    ##    <int> <fct>                      <fct>                                                              <dbl>
    ##  1  2019 Asian and Pacific Islander Diseases of Heart (I00-I09, I11, I13, I20-I51)                       623
    ##  2  2019 Asian and Pacific Islander Malignant Neoplasms (Cancer: C00-C97)                                582
    ##  3  2019 Asian and Pacific Islander All Other Causes                                                     401
    ##  4  2019 Asian and Pacific Islander Cerebrovascular Disease (Stroke: I60-I69)                            110
    ##  5  2019 Asian and Pacific Islander Diabetes Mellitus (E10-E14)                                           78
    ##  6  2019 Asian and Pacific Islander Influenza (Flu) and Pneumonia (J09-J18)                               67
    ##  7  2019 Asian and Pacific Islander Essential Hypertension and Renal Diseases (I10, I12)                  66
    ##  8  2019 Asian and Pacific Islander Alzheimer's Disease (G30)                                             50
    ##  9  2019 Asian and Pacific Islander Accidents Except Drug Poisoning (V01-X39, X43, X45-X59, Y85-Y86)      40
    ## 10  2019 Asian and Pacific Islander Chronic Lower Respiratory Diseases (J40-J47)                          40
    ## 11  2019 Asian and Pacific Islander Intentional Self-Harm (Suicide: U03, X60-X84, Y87.0)                  40

For Asian and Pacific Islander, the number one cause if Diseases of
Heart with average 623 deaths. This is followed by Cancer and
Cerebrovascular Disease.

# Share

I will use the same data to plot

# Conclusion
