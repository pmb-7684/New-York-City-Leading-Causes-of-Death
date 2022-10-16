NYC Leading Causes of Death
================
Paula Bailey
2022-10-14

# Background

Looking at my profile, one may believe two things about me - 1. I love
large cities like Chicago and New York. 2. I’m only interested in health
data set. I haven’t been to either city since I was a child; however,
both cities have wealth of open source data. The city of Chicago has
their [portal](https://data.cityofchicago.org/). I just discovered New
York City’s [NYCOpenData](https://opendata.cityofnewyork.us/). This site
also has an API, so I plan to practice accessing APIs in the near
future. As for the second question, it’s more likely because I can find
more data on the subject of health and crime.

This data comes from the NYC OpenData website. It is on the leading
causes of death in New York City by sex and ethnicity since 2007. The
cause of death is derived from the NYC death certificate which is issued
for every death that occurs in the city”.

After I complete this project, I will be completing it again in python.
Currently, I am in school and R is the language of choice. But I
realize, it is important to know python….practice…practice…

# Ask

This is not a formal project, so there is not a detailed business task
(or statement). I will use the data set to explore, complete some
summary statistics and visualizations.

I have the following questions. What was the cause of death for women in
NYC? What about the top 10 causes? Does race change the top cause for
women? Basically, is it different for Black, White, Hispanic, and Asian
women?

# Prepare

This csv file is named `New_York_City_leading_Causes_of_Death`. It
contains 1,272 rows and 7 features where each row is a cause of death.
It is contained on the NYC OpenData platform where it is updated
annually by Department of Health and Mental Hygiene (DOHMH). It was last
updated on February 8, 2022. The data is from 2007 to 2019.

The csv has the following features:

| column Name             | description                                 | definitions             |
|-------------------------|---------------------------------------------|-------------------------|
| ï..Year                 | Year of Death                               | year                    |
| leading_cause           | the unique cause of death                   | Leading Cause           |
| Sex                     | Sex of decedent                             |                         |
| race/ethnicity          | Race of decedent                            |                         |
| Deaths                  | Number of People who died due to this cause |                         |
| death_rate              | Death rate within sex and race category     |                         |
| age_adjusted_death_rate | Age adjusted death rate within sex and race | Age Adjusted Death Rate |

Based on the information from the website all of the variables are in
plain text. So, we will need to verify the types of each column after we
import the data.

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
nycCauses <- read.csv("New_York_City_Leading_Causes_of_Death_pmb.csv", na.strings=c("",".","NA"))
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
    ##    Sex Race.Ethnicity Deaths Death.Rate Age.Adjusted.Death.Rate
    ## 1 Male       Hispanic   1603  136.80299               176.78329
    ## 2 Male       Hispanic   1164   99.33792               121.58177
    ## 3 Male       Hispanic    423   36.09960                35.70790
    ## 4 Male       Hispanic    245   20.90875                25.40934
    ## 5 Male       Hispanic    182   15.53222                19.85821
    ## 6 Male       Hispanic    182   15.53222                19.55838

Next, the structure of the file.

``` r
str(nycCauses)
```

    ## 'data.frame':    1272 obs. of  7 variables:
    ##  $ ï..Year                : int  2019 2019 2019 2019 2019 2019 2019 2019 2019 2019 ...
    ##  $ Leading.Cause          : chr  "Diseases of Heart (I00-I09, I11, I13, I20-I51)" "Malignant Neoplasms (Cancer: C00-C97)" "Mental and Behavioral Disorders due to Accidental Poisoning and Other Psychoactive Substance Use (F11-F16, F18-"| __truncated__ "Diabetes Mellitus (E10-E14)" ...
    ##  $ Sex                    : chr  "Male" "Male" "Male" "Male" ...
    ##  $ Race.Ethnicity         : chr  "Hispanic" "Hispanic" "Hispanic" "Hispanic" ...
    ##  $ Deaths                 : int  1603 1164 423 245 182 182 174 171 170 135 ...
    ##  $ Death.Rate             : num  136.8 99.3 36.1 20.9 15.5 ...
    ##  $ Age.Adjusted.Death.Rate: num  176.8 121.6 35.7 25.4 19.9 ...

As mentioned above, everything is a character format except for year,
including deaths, death.rate, and the adjusted death rate.

I think we should make the following data changes:

-   Leading Cause, Sex, and Race.Ethnicity from CHR to FACTOR
-   Leading Cause, Sex, and Race.Ethnicity from CHR to FACTOR
-   death rates and age adjusted deaths rates from CHR to DOUBLE
-   Change ï..Year to year (column name)

After our data type changes, our data sets will contain 3 continuous
features, 1 discrete feature and 3 categorical features.

Rename column ï..Year

``` r
nycCauses <- nycCauses %>% rename(year=ï..Year)
```

Before changing data types, let’s look for any NAs.

During this process, I ran into
`Warning: NAs introduced by coercionWarning` which indicates the data
set has missing information (empty not NA). Hmm, that is interesting. I
was not expecting to see NAs. I resolved this issue by adding
`na.strings=c("",".","NA")` during import.

``` r
colSums(is.na(nycCauses))
```

    ##                    year           Leading.Cause                     Sex          Race.Ethnicity 
    ##                       0                       0                       0                       0 
    ##                  Deaths              Death.Rate Age.Adjusted.Death.Rate 
    ##                     138                     453                     453

After reviewing the data set, we can see which descendants have NAs.

-   Descendants with “Other” as race do not have Death rate and age
    adjusted death rate.
-   The remaining 138 descendants with missing deaths are a combination
    of with “other Race” and “Not Stated” as race.

Based on the above, I will create a data set descendants with missing
deaths removed. We will name that data frame as `nycCauses_Deaths`. I
will try to complete most of the analysis with that data set. Recall the
original data set is 1,272 observations and now we have 1,134. The
difference is 138.

``` r
nycCauses_Deaths <- nycCauses %>% drop_na(Deaths)
```

Change varaibles Leading causes, sex, and race to factors. Change
deaths, death,rate and age adjusted death rate to integer and double,
respectively.

``` r
# update to factors
nycCauses_Deaths$Leading.Cause = factor(nycCauses_Deaths$Leading.Cause)
nycCauses_Deaths$Sex = factor(nycCauses_Deaths$Sex)
nycCauses_Deaths$Race.Ethnicity = factor(nycCauses_Deaths$Race.Ethnicity)

# update to integer and double
nycCauses_Deaths$Deaths = as.integer(nycCauses_Deaths$Deaths)
nycCauses_Deaths$Death.Rate = as.double(nycCauses_Deaths$Death.Rate)
nycCauses_Deaths$Age.Adjusted.Death.Rate = as.double(nycCauses_Deaths$Age.Adjusted.Death.Rate)
```

We will also create a second data set named`nycCause_noNA`, where we
remove all NAs which will have 819 observations.

``` r
nycCauses_noNA   <- nycCauses_Deaths %>% drop_na(Death.Rate, Age.Adjusted.Death.Rate)
```

Let’s confirm our changes

``` r
str(nycCauses_Deaths)
```

    ## 'data.frame':    1134 obs. of  7 variables:
    ##  $ year                   : int  2019 2019 2019 2019 2019 2019 2019 2019 2019 2019 ...
    ##  $ Leading.Cause          : Factor w/ 32 levels "Accidents Except Drug Poisoning (V01-X39, X43, X45-X59, Y85-Y86)",..: 18 25 26 17 21 9 1 12 14 19 ...
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
    ##                   NA's   :315      NA's   :315

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

Let’s look at the distinct values for our character values -
Leading_Cause, Sex and Race.Ethnicity. I noticed in the str() above for
sex, there are four factors listed. I want to make sure those factors
are unique.

``` r
n_distinct(nycCauses_Deaths$year)
```

    ## [1] 9

``` r
n_distinct(nycCauses_Deaths$Sex)
```

    ## [1] 4

``` r
n_distinct(nycCauses_Deaths$Leading.Cause)
```

    ## [1] 32

``` r
n_distinct(nycCauses_Deaths$Race.Ethnicity)
```

    ## [1] 8

``` r
unique(nycCauses_Deaths[["Sex"]])
```

    ## [1] Male   Female M      F     
    ## Levels: F Female M Male

Wow…As we can see,it’s a good thing that we looked. We have Male, M,
Female and F. So, we will change the factor of F and M to Female and
Male. Since we already created nycCauses_noNA, we will update that files
as well.

``` r
levels(nycCauses_Deaths$Sex)[levels(nycCauses_Deaths$Sex)=='M'] <- 'Male'
levels(nycCauses_Deaths$Sex)[levels(nycCauses_Deaths$Sex)=='F'] <- 'Female'

levels(nycCauses_noNA$Sex)[levels(nycCauses_noNA$Sex)=='M'] <- 'Male'
levels(nycCauses_noNA$Sex)[levels(nycCauses_noNA$Sex)=='F'] <- 'Female'
```

``` r
unique(nycCauses_Deaths[["year"]])
```

    ## [1] 2019 2014 2013 2007 2009 2008 2011 2012 2010

We notice that we are missing data from 2015 - 2018. I’m not sure why we
are missing data. The data dictionary in the `Cause_of_Death_121412.csv`
file states years from 2007 - 2016 and the title gives the impression we
will have data from 2007. We have an discrepancy, but we will carry on.

Look at factors for ethnicity.

``` r
unique(nycCauses_Deaths[["Race.Ethnicity"]])
```

    ## [1] Hispanic                   Asian and Pacific Islander Non-Hispanic White        
    ## [4] Non-Hispanic Black         Other Race/ Ethnicity      Not Stated/Unknown        
    ## [7] White Non-Hispanic         Black Non-Hispanic        
    ## 8 Levels: Asian and Pacific Islander Black Non-Hispanic Hispanic Non-Hispanic Black ... White Non-Hispanic

As a best practice, it is good to check all levels of features with
factors. Above we have both Black Non-Hispanic and Non-Hispanic Black
and White Non-Hispanic and Non-Hispanic White, We need to address this
so it will not effect our work.

``` r
levels(nycCauses_Deaths$Race.Ethnicity)[levels(nycCauses_Deaths$Race.Ethnicity)=='Non-Hispanic Black'] <- 'Black Non-Hispanic'
levels(nycCauses_Deaths$Race.Ethnicity)[levels(nycCauses_Deaths$Race.Ethnicity)=='Non-Hispanic White'] <- 'White Non-Hispanic'


levels(nycCauses_noNA$Race.Ethnicity)[levels(nycCauses_noNA$Race.Ethnicity)=='Non-Hispanic Black'] <- 'Black Non-Hispanic'
levels(nycCauses_noNA$Race.Ethnicity)[levels(nycCauses_noNA$Race.Ethnicity)=='Non-Hispanic White'] <- 'White Non-Hispanic'
```

Check to confirm our changes

``` r
unique(nycCauses_Deaths[["Sex"]])
```

    ## [1] Male   Female
    ## Levels: Female Male

``` r
unique(nycCauses_Deaths[["Race.Ethnicity"]])
```

    ## [1] Hispanic                   Asian and Pacific Islander White Non-Hispanic        
    ## [4] Black Non-Hispanic         Other Race/ Ethnicity      Not Stated/Unknown        
    ## 6 Levels: Asian and Pacific Islander Black Non-Hispanic Hispanic White Non-Hispanic ... Other Race/ Ethnicity

Now our data sets seem to be clean. As mentioned above we will use
nycCauses_Deaths and nycCauses_noNA. We will mainly use
nycCauses_Deaths, because we were able to maintain the most data.

# Analyze

Question 1 - For black women who died in 2019, how many women died from
heart disease or diabetes?

``` r
nycCauses_Deaths %>% 
  group_by(year, Leading.Cause, Sex) %>% 
  summarise(mean = mean(Deaths)) %>%
  filter(year == 2019 & Sex == "Female" & grepl('Heart|Diabetes', Leading.Cause))
```

    ## # A tibble: 2 x 4
    ## # Groups:   year, Leading.Cause [2]
    ##    year Leading.Cause                                  Sex     mean
    ##   <int> <fct>                                          <fct>  <dbl>
    ## 1  2019 Diabetes Mellitus (E10-E14)                    Female  129.
    ## 2  2019 Diseases of Heart (I00-I09, I11, I13, I20-I51) Female 1266.

In 2019, on average 129 black women died from diabetes and 1,266 died
from heart disease.

Question 2 - In 2019, what is the top causes of death for women?

``` r
  nycCauses_Deaths %>% 
        group_by(year, Leading.Cause, Sex) %>% 
        summarise(average = mean(Deaths)) %>%
        filter(year == 2019 & Sex == "Female") %>%
        arrange(desc(average)) %>%
        select(Leading.Cause, average) %>%
        head(10)
```

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

The top cause of death for women is again heart disease with an average
of 1,266 deaths.

Question 3 - Question two leads us to ask, Is heart disease the leading
cause of death for all race/ethnicity in NYC in 2019? Is it a leading
cause by race? We will look at Black, White, Hispanic, Asian and Pacific
Islander and Other Race.

``` r
nycCauses_Deaths %>% 
      group_by(year, Race.Ethnicity,Leading.Cause) %>% 
      filter(year == 2019 & Sex == "Female" & Race.Ethnicity == 'Black Non-Hispanic') %>%
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
    ##  1  2019 Black Non-Hispanic Diseases of Heart (I00-I09, I11, I13, I20-I51)                               2483
    ##  2  2019 Black Non-Hispanic Malignant Neoplasms (Cancer: C00-C97)                                        1753
    ##  3  2019 Black Non-Hispanic All Other Causes                                                             1578
    ##  4  2019 Black Non-Hispanic Diabetes Mellitus (E10-E14)                                                   383
    ##  5  2019 Black Non-Hispanic Cerebrovascular Disease (Stroke: I60-I69)                                     316
    ##  6  2019 Black Non-Hispanic Essential Hypertension and Renal Diseases (I10, I12)                          290
    ##  7  2019 Black Non-Hispanic Chronic Lower Respiratory Diseases (J40-J47)                                  242
    ##  8  2019 Black Non-Hispanic Influenza (Flu) and Pneumonia (J09-J18)                                       227
    ##  9  2019 Black Non-Hispanic Alzheimer's Disease (G30)                                                     171
    ## 10  2019 Black Non-Hispanic Mental and Behavioral Disorders due to Accidental Poisoning and Other Ps~     120
    ## 11  2019 Black Non-Hispanic Nephritis, Nephrotic Syndrome and Nephrisis (N00-N07, N17-N19, N25-N27)        96

The top cause of death for Black females in 2019 was Diseases of Heart
(I00-I09, I11, I13, I20-I51) with an average of 2,483 women dying. The
second highest is Malignant Neoplasms (Cancer: C00-C97) with 1,753
cases.

``` r
nycCauses_Deaths %>% 
      group_by(year, Race.Ethnicity,Leading.Cause) %>% 
      filter(year == 2019 & Sex == "Female" & Race.Ethnicity == 'White Non-Hispanic') %>%
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
    ##  1  2019 White Non-Hispanic Diseases of Heart (I00-I09, I11, I13, I20-I51)                               4001
    ##  2  2019 White Non-Hispanic Malignant Neoplasms (Cancer: C00-C97)                                        2655
    ##  3  2019 White Non-Hispanic All Other Causes                                                             2264
    ##  4  2019 White Non-Hispanic Chronic Lower Respiratory Diseases (J40-J47)                                  480
    ##  5  2019 White Non-Hispanic Cerebrovascular Disease (Stroke: I60-I69)                                     390
    ##  6  2019 White Non-Hispanic Alzheimer's Disease (G30)                                                     341
    ##  7  2019 White Non-Hispanic Influenza (Flu) and Pneumonia (J09-J18)                                       308
    ##  8  2019 White Non-Hispanic Essential Hypertension and Renal Diseases (I10, I12)                          228
    ##  9  2019 White Non-Hispanic Diabetes Mellitus (E10-E14)                                                   197
    ## 10  2019 White Non-Hispanic Accidents Except Drug Poisoning (V01-X39, X43, X45-X59, Y85-Y86)              167
    ## 11  2019 White Non-Hispanic Mental and Behavioral Disorders due to Accidental Poisoning and Other Ps~     125

For Non-Hispanic White women, the number one cause is Diseases of Heart
with average 4,001 deaths. This is followed by Cancer and Chronic Lower
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

For Hispanic women, the number one cause of death is Diseases of Heart
with average 1,574 deaths. This is followed by Cancer and
Cerebrovascular Disease.

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

For Asian and Pacific Islander women, the number one cause of death is
Diseases of Heart with average 623 deaths. This is followed by Cancer
and Cerebrovascular Disease.

``` r
nycCauses_Deaths %>% 
      group_by(year, Race.Ethnicity,Leading.Cause) %>% 
      filter(year == 2019 & Sex == "Female" & Race.Ethnicity == 'Other Race/ Ethnicity') %>%
      summarise(average = mean(Deaths)) %>%
      arrange(desc(average)) %>%
      select(Race.Ethnicity, Leading.Cause, average) 
```

    ## `summarise()` has grouped output by 'year', 'Race.Ethnicity'. You can override using the `.groups` argument.
    ## Adding missing grouping variables: `year`

    ## # A tibble: 23 x 4
    ## # Groups:   year, Race.Ethnicity [1]
    ##     year Race.Ethnicity        Leading.Cause                                                    average
    ##    <int> <fct>                 <fct>                                                              <dbl>
    ##  1  2019 Other Race/ Ethnicity Diseases of Heart (I00-I09, I11, I13, I20-I51)                      34.5
    ##  2  2019 Other Race/ Ethnicity Malignant Neoplasms (Cancer: C00-C97)                               26  
    ##  3  2019 Other Race/ Ethnicity All Other Causes                                                    22.5
    ##  4  2019 Other Race/ Ethnicity Diabetes Mellitus (E10-E14)                                          7.5
    ##  5  2019 Other Race/ Ethnicity Cerebrovascular Disease (Stroke: I60-I69)                            5.5
    ##  6  2019 Other Race/ Ethnicity Essential Hypertension and Renal Diseases (I10, I12)                 3.5
    ##  7  2019 Other Race/ Ethnicity Influenza (Flu) and Pneumonia (J09-J18)                              3.5
    ##  8  2019 Other Race/ Ethnicity Alzheimer's Disease (G30)                                            3  
    ##  9  2019 Other Race/ Ethnicity Assault (Homicide: U01-U02, Y87.1, X85-Y09)                          3  
    ## 10  2019 Other Race/ Ethnicity Accidents Except Drug Poisoning (V01-X39, X43, X45-X59, Y85-Y86)     2  
    ## # ... with 13 more rows

For women who identified as other race, Diseases of Heart is also the
top cause of death with average 623 deaths. This is followed by Cancer
and Diabetes Mellitus.

# Share

This plot shows that heart disease has been trending down since 2007 as
the cause of death for women. However, it still seems to +be the number
one cause of death for women.

``` r
nycCauses_Deaths %>% 
        filter(Sex == "Female" & Leading.Cause == 'Diseases of Heart (I00-I09, I11, I13, I20-I51)') %>%
  
       ggplot( aes(x=year, y = Deaths)) + 
        theme(axis.text.x=element_text(angle=50, hjust=1)) +
        geom_bar(stat = "identity")+
        facet_wrap(~year)
```

![](README_files/figure-gfm/unnamed-chunk-75-1.png)<!-- -->

I have added images of two additional plots that are contained in a file
named `Tableau_images.doc.` Those plots show heart disease has been
declining since 2007, but continues to be the number one cause of death
for men and women.

# Conclusion

As mentioned, I plan to begin the project using python; however this
time I will look at the causes of death for men. I will also be using
the data to complete plots (charts) in Tableau. Again, another way to
practice.

In the meantime, we can see that heart disease has been the leading
cause of death for women in the NYC area. This is regardless their
ethnicity.
