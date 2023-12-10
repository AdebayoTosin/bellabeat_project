# bellabeat_project

## **Disclaimer:** This is not real project but a capstone project from Google Professional Certificate for the purppose of learning, The Aim is to demostrate my R programming skill

---
- title: "How can Bellabeat wellness Technology Company Play it Smart"
- author: "Tosin Emmanuel"
- date: "2023-10-28"
- output: html_document
---

# STATEMENT OF BUSINESS TASK

This project focus on how to improve the marketing strategy of bellabeat products 
by using data to gain insight on how users use non bellabeat smart device,
which will unlock new growth opportunity for the company to ensure 
the stakeholders make best and inform decisions through our recommendations.

The stakeholderS;

-   URska Srsen - Bellabeat cofounder and chief creative officer
-   Sando Mur - Bellabeat Cofounder and key member of Bellabeat executive team
-   Bellabeat Marketing Analytic Team

### DESCRIPTION OF THE DATA SOURCE USED

FitBit Fitness Tracker Data (CC0: Public Domain, dataset made available through Mobius): 
This Kaggle data set contains personal fitness tracker from thirty fitbit users.
Thirty eligible Fitbit users consented to the submission of personal tracker data, 
including minute-level output for physical activity, heart rate, and sleep monitoring.
It includes information about daily activity, steps,sleep and heart rate that can be used to explore users' habits [link](https://www.kaggle.com/datasets/arashnic/fitbit)

The data is long dataset,and it is sorted without a missing values,all missing values as been replaced with 0.

The limitation on the data are;

-   we have data of 32 users and we are not sure if they have the proper representation of the whole population
as the demographic information are not in the data, we may have sampling bias
-   Also the data is not current and is limited to just 2month of survey records

  **Disclaimer** this is not a private data has this is choosen by Google for learning purposes.

## CLEANING AND DATA MANIPULATION PROCESSES

I will focus my analysis in R due to the size of the data and also to demostrate my skills 

### Installing packages and opening libraries

we will use the following packages to carry out our analysis

-   tidyverse
-   here
-   skimr
-   janitor
-   lubricate
-   ggplot2

```{r install packages, warning=FALSE}
library(tidyverse)
library(here)
library(skimr)
library(janitor)
library(lubridate)
library(ggplot2)
```

### Importing datasets

In this analysis we will be focusing dataset with daily time frame records,
because intentifying trends on high time frame are more reliable.
Here are following dataset we will import;

-   dailyCalories_merged
-   dailyIntensities_merged
-   dailySteps_merged
-   sleepDay_merged
-   weightloginfo_merged

```{r importation of dataset}
dailyCalories_merged <- read_csv("C:/Users/Busy Fingers/Downloads/bellabeat/Fitabase Data 4.12.16-5.12.16/dailyCalories_merged.csv")
dailyIntensities_merged <- read_csv("C:/Users/Busy Fingers/Downloads/bellabeat/Fitabase Data 4.12.16-5.12.16/dailyIntensities_merged.csv")
dailySteps_merged <- read_csv("C:/Users/Busy Fingers/Downloads/bellabeat/Fitabase Data 4.12.16-5.12.16/dailySteps_merged.csv")
sleepDay_merged <- read_csv("C:/Users/Busy Fingers/Downloads/bellabeat/Fitabase Data 4.12.16-5.12.16/sleepDay_merged.csv")
weightLogInfo_merged2 <- read_csv("C:/Users/Busy Fingers/Downloads/bellabeat/Fitabase Data 4.12.16-5.12.16/weightLogInfo_merged2.csv")

dailycalories <- dailyCalories_merged
dailyintensity<- dailyIntensities_merged
dailysteps <- dailySteps_merged
sleepday <- sleepDay_merged
weightlog <- weightLogInfo_merged2
```

### Preview our dataset

```{r preview Dataset}

head(dailycalories)
str(dailycalories)


head(dailyintensity)
str(dailyintensity)

head(dailysteps)
str(dailysteps)

head(sleepday)
str(sleepday)

head(weightlog)
str(weightlog)
```

### Veryfying number of users and removing duplication

As i continue with my cleaning i have to confirm if there is any N/A value or duplication in my data

#### Comfirm the number of unique value in each data set

```{r data cleaning}
length(unique(dailycalories$Id))
length(unique(dailyintensity$Id))
length(unique(dailysteps$Id))
length(unique(sleepday$Id))
length(unique(weightlog$Id))
```

#### Number of ducplicated rows in each data set

```{r data cleaning processes}
sum(duplicated(dailycalories))
sum(duplicated(dailyintensity))
sum(duplicated(dailysteps))
sum(duplicated(sleepday))
sum(duplicated(weightlog))
```

#### Removing the duplicated rows

```{r data cleaning process}
dailycalories <- dailycalories %>% 
  distinct() %>% 
  drop_na()

dailyintensity <- dailyintensity %>% 
  distinct() %>% 
  drop_na()

dailysteps <- dailysteps %>% 
  distinct() %>% 
  drop_na()

sleepday <- sleepday %>% 
  distinct() %>% 
  drop_na()

weightlog <- weightlog %>% 
  distinct() %>% 
  drop_na()
```

## Analysing and Exploring Data

### Fixingg Formatting and adding Weekday to our dataset

```{r fixing formatting}
dailycalories <-dailyCalories_merged %>% 
  mutate(ActivityDay = mdy(ActivityDay) %>% 
  as.Date()) %>%
  mutate(dayofweek = weekdays(ActivityDay))
head(dailycalories)

dailyintensity <- dailyIntensities_merged %>%
  mutate(ActivityDay = mdy(ActivityDay) %>% 
  as.Date()) %>% 
  mutate(dayofweek = weekdays(ActivityDay))
head(dailyintensity)

dailysteps <- dailySteps_merged %>% 
  mutate(ActivityDay = mdy(ActivityDay) %>% 
  as.Date()) %>%
  mutate(dayofweek = weekdays(ActivityDay))
head(dailysteps)
```

### Summary of Analysis

```{r data summary}
dailycalories %>% 
  select(Calories) %>% 
  summary()

dailyintensity %>% 
  select(SedentaryMinutes,LightActiveDistance, VeryActiveMinutes) %>% 
summary()

dailysteps %>% 
  select(StepTotal) %>% 
  summary()

sleepday %>% 
  select(TotalMinutesAsleep,TotalTimeInBed) %>% 
  summary()

weightlog %>% 
  select(WeightKg) %>% 
  summary()
```

Observation from Analysis are;

-   Majority of activities are sedentary which is 991 minites
-   On the average the participant sleep for 7 hours
-   average step per day is 7638 which is abit low
-   they burn 2304 calories perday on the average, 
general study says 2000 is the minimum but with future research we can determin the average.

### VISUALIZATION

```{r dailysteps vs dailycalories}
merged_data <- merge(dailycalories, dailysteps, by = c('Id', 'dayofweek'))

ggplot(data = merged_data)+
  geom_point(mapping = aes(x=StepTotal, y=Calories))+
  geom_smooth(mapping = aes(x=StepTotal, y=Calories))+
  labs(title = "Dailysteps vs Daily Calories")
```
![image](https://github.com/AdebayoTosin/bellabeat_project/blob/main/Dailysteps%20vs%20Dailycalories.png)

dailysteps vs weighlog chart: this is visualize the correlation between dailystep and weight.

```{r daily steps vs weighhtlog}

merged_data2 <- merge(dailysteps, weightlog, by = c('Id'))

  ggplot(data = merged_data2)+
  geom_smooth(mapping = aes(x=StepTotal, y=WeightKg))+
    labs(title = "Dailysteps vs Weightinfo", subtitle = "Correlation between Total dailysteps and weightKg")
```
![image](https://github.com/AdebayoTosin/bellabeat_project/blob/main/Dailystepvsweightinfo.png)

dailysteps by Weekdays charts : this visualization demonstrate that Tuesday has the lowest activities

```{r dailysteps by weeksdays}
ggplot(data = dailysteps)+
  geom_bar(mapping = aes(x=dayofweek,fill=dayofweek))+
  labs(title = "DailySteps by weekdays chart")
```
![image](https://github.com/AdebayoTosin/bellabeat_project/blob/main/dailycalories%20vs%20weekdays.png)

Daily caalories by weekdays charts: this visualization demonstrate that there is low burn of calories on Tuesday 
which demonstrate further more correlation between calories and daily step

```{r dailycalories by weekday}
ggplot(data = dailycalories)+
  geom_bar(mapping = aes(x=dayofweek,fill=dayofweek))+
  labs(title = "Daily Calories by Weekdays chart")
```
![image](https://github.com/AdebayoTosin/bellabeat_project/blob/main/dailycalories%20vs%20weekdays.png)

## Summary of key findings

-   There is correlation between daily steps and daily calories
-   There is correlation between steptotal and weightkg
-   There is low activity on Tuesdays as demonstrated by the dailysteps vs weekday and daily calories by weekday respectively
-   generally the sleeping time per day is low compare to recommended hours
-   There is wide margin between sedentary minutes and active minutes, 991.2 and 21.16 respectively.

## Recommendation

it is important to note I have limitation on the datasets of 32 participants in the survey activities 
and also I am not given there demographic information. 
To this effect I am not sure if the survey represent the total population,therefore, our findings might be bias.

Here are the insight I got from the Analysis;

* My Analysis show strong correlation between dailysteps,daily calories burn and weight loss. 
Also summary shows average Total steps of 7638 is low compare to CDC average, 
They found that taking 8,000 steps per day was associated with a 51% lower risk for all-cause mortality (or death from all causes).
Taking 12,000 steps per day was associated with a 65% lower risk compared with taking 4,000 steps.

secondly there is wide margin between sedentary activities and active activities 991.2 and 21.16 respectively,
this shows that there is room for improvement for users on average daily steps.

I recommend constant reminder and users record on Bellabeat App, 
also creation of competition and reward systems for Bellabeat memberships for high performing members,
this will be a source of motivation for users and could even further promote adoption of the bellabeat product by non users

* My Analysis show there is low dailystep, daily calories burn on Tuesdays,
but with more survey and data we can investigate further on why and how we can leverage on it.
