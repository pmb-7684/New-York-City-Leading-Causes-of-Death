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
for every death that occurs in the city”

After I complete this project, I will be completing it again in python.
Currently, I am in school and R is the language of choice. But I
realize, it is important to know python. I’ve completed a program on the
DataCamp platform…practice…practice…

# Ask

This is not a formal project, so there is not a detailed business task
(or statement). I will use the data set to explore, complete some
summary statistics and visualizations.

# Prepare

This csv file is named `New_York_City_leading_Causes_of_Death`. It’s in
a long format with a shape of 1,272 x 7 where each row is a cause of
death. It is contained on the NYC OpenData platform where it is updated
annually by Department of Health and Mental Hygiene (DOHMH). It was last
updated on February 8, 2022.

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

I think we should make the following data changes: \* Year changes from
INT to DATE \* Leading Cause, Sex, and Race.Ethnicity from CHR to FACTOR
\* deaths from CHR to INT \* death rates and age adjusted deaths rates
from CHR to DOUBLE
