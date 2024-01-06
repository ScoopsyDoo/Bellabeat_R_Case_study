# Bellabeat R Case Study
### Author: Sarah Cooper
### Date: January 3, 2024

# ❓ [ASK](#-ask)
# 💻 [PREPARE](#-prepare)
# ⚙️ [PROCESS](#-process)
# 📈 [ANALYZE](#-analyze)
# 📋 [SHARE](#-share)
# 🎬 [ACT](#-act)

![Bellabeat logo pink on white background](https://user-images.githubusercontent.com/77591203/196562658-bfe5df3b-4e68-4c4e-97b8-d9c057d28dec.jpg)

## Introduction
Bellabeat is a technology company that designs and manufactures health-focused products exclusively for women. The company was founded in 2013 to collect and analyze data on women's activity levels, stress, sleep patterns, and overall health. This data enables women to make informed decisions about their health and habits. Bellabeat can become a significant player in the smart device market. In 2016, the company opened new offices and launched its products, which are available on its website (https://bellabeat.com/) and through online retailers. Bellabeat has invested in multiple media channels and has mainly focused on digital marketing, including popular platforms like Instagram, Facebook, YouTube, Twitter, and Google Search.

# [ASK](#-ask)

### Business Task
The business task was to analyze smart device usage data to gain insights into how consumers use non-Bellabeat smart devices. Then, select one Bellabeat product to apply these insights to in the presentation. The business questions are:

1. What are some trends in smart device usage?

2. How could these trends apply to Bellabeat customers?

3. How could these trends help influence Bellabeat's marketing strategy?

#### Key Stakeholders
- **Urška Sršen**: Bellabeat's co-founder and Chief Creative Officer
* **Sando Mur**: Mathematician and Bellabeat’s co-founder; key member of the Bellabeat executive team
+ **Bellabeat marketing analytics team**: A team of data analysts responsible for collecting, analyzing, and reporting
data that helps guide Bellabeat's marketing strategy. I am in the role of a junior analyst on the team

#### Products
- **Bellabeat app**: The app provides users with data about their activity, sleep, stress, menstrual cycle, and mindfulness habits and helps people better understand their health and habits. The app connects to their line of smart wellness products.

* **Leaf**: a wellness tracker can be worn as a bracelet, necklace, or clip. Leaf can connect with the Bellabeat app to track activity, sleep, and stress.

+ **Time**: A wellness watch can track activity, sleep, and stress and connect with the Bellabeat app. A watch provides you with insights about your daily wellness.
  
- **Spring**: A water bottle tracks daily water intake to ensure you are hydrated enough daily by applying state-of-the-art innovative technology. Spring can connect with the app and track your hydration level.

* **Bellabeat membership**: a subscription-based program for users with 24/7 access to fully personalized guidance on nutrition, activity, sleep, health, beauty, and mindfulness-based on their lifestyle and goals.

# PREPARE
The data being used in this case study can be found here: [FitBit Fitness Tracker Data CC0](https://www.kaggle.com/datasets/arashnic/fitbit): Public Domain, dataset made available through [Mobius](https://www.kaggle.com/arashnic)

The data is stored and uploaded in R Studio. This Kaggle data set contains personal fitness tracker data from thirty Fitbit users. Thirty eligible Fitbit users consented to the submission of personal tracker data, including minute-level output for physical activity, heart rate, and sleep monitoring. It includes information about daily activity, steps, and heart rate that can be used to explore users' habits.

The data set contains 18 CSV files organized in long format. Below is a breakdown of the data using the ROCCC approach:

- **Reliability - LOW**: The data comes from 30 Fitbit users who consented to the submission of personal tracker data, including minute-level output for physical activity, heart rate, and sleep monitoring. However, no demographic information is included.

- **Originality - LOW**: Third-party data collected using Amazon Mechanical Turk.

- **Comprehensive - MED**: The dataset contains multiple fields on daily activity intensity, calories used, daily steps taken, daily sleep time, and weight record. Data is within the parameters required for the Bellabeat’s business task

- **Current - LOW**: This data is from March 2016 through May 2016.

- **Cited — LOW**: The third-party dataset was available by Mobius via Kaggle.

# PROCESS

### Packages

```
## Install packages##
install.packages("tidyverse")
install.packages("dplyr")
install.packages("janitor")
install.packages("lubridate")
install.packages("ggplot2")
install.packages("tidyr")
library(tidyverse)
library(dplyr)
library(janitor)
library(lubridate)
library(ggplot2)
library(tidyr)
```
### Imports and Data Frames

```
## data frames
setwd("/cloud/project/Fitabase Data 4.12.16-5.12.16")
getwd()
activity <- read_csv("dailyActivity_merged.csv")
calories <- read_csv("dailyCalories_merged.csv")
sleep <- read_csv("sleepDay_merged.csv")
steps <- read_csv("dailySteps_merged.csv")
weight <- read_csv("weightLogInfo_merged.csv")
heartrate <- read_csv("heartrate_seconds_merged.csv")
```
Now that I have imported all of the data frames I will be using, I am ready to start cleaning the data. I will take a look at each data frame to familiarize myself with the data and to check for errors. Below is a sample.
```
## Cleaning and exploring data
head(activity)
colnames(activity)
```
```
  Id ActivityDate TotalSteps TotalDistance TrackerDistance
1 1503960366    4/12/2016      13162          8.50            8.50
2 1503960366    4/13/2016      10735          6.97            6.97
3 1503960366    4/14/2016      10460          6.74            6.74
4 1503960366    4/15/2016       9762          6.28            6.28
5 1503960366    4/16/2016      12669          8.16            8.16
6 1503960366    4/17/2016       9705          6.48            6.48

```
While cleaning, I noticed some duplicate data and cleaned that, as well as changing everything to lowercase for ease.
```
sum(duplicated(sleep))
sleep <- unique(sleep)
activity <- rename_with(activity, tolower)
sleep <- rename_with(sleep, tolower)
```
Upon looking at the timestamp data, I noticed some problems. To solve this, I will convert it to date time format.
```
activity$activitydate=as.POSIXct(activity$activitydate, format="%m/%d/%Y", tz=Sys.timezone())
activity$date <- format(activity$activitydate, format = "%m/%d/%y")

sleep$sleepday=as.POSIXct(sleep$sleepday, format="%m/%d/%Y %I:%M:%S %p", tz=Sys.timezone())
sleep$date <- format(sleep$sleepday, format = "%m/%d/%y")
```
# ANALYZE
To begin the analysis phase, I will first see how many participants there are in each category.
```
## Finding the number of participants in each category
n_distinct(activity$Id)  
n_distinct(calories$Id)   
n_distinct(steps$Id)
n_distinct(weight$Id)
n_distinct(heartrate$Id)
```
  
+ 33  

+ 33

+ 33

+ 8

+ 14

To summarize the above data, there are 33 participants in the activity, calories, and steps datasets,14 in heart rate, and only 8 in the weight dataset. The fact that there are only 8 participants in the weight dataset means that more data would be needed to make a strong recommendation or conclusion.

```
## Checking for significant changes in weight
weight%>%
  group_by(Id)%>%
  summarise(min(WeightKg),max(WeightKg))
```
```
# A tibble: 8 × 3
          Id `min(WeightKg)` `max(WeightKg)`
       <dbl>           <dbl>           <dbl>
1 1503960366            52.6            52.6
2 1927972279           134.            134. 
3 2873212765            56.7            57.3
4 4319703577            72.3            72.4
5 4558609924            69.1            70.3
6 5577150313            90.7            90.7
7 6962181067            61              62.5
8 8877689391            84              85.8
```
There are no significant changes in weight coming from these 8 participants. Due to the combination of low sample size and lack of variable change
```
# activity
activity %>%  
  select(totalsteps,
         totaldistance,
         sedentaryminutes, calories) %>%
  summary()
```
```
  totalsteps    totaldistance    sedentaryminutes    calories   
 Min.   :    0   Min.   : 0.000   Min.   :   0.0   Min.   :   0  
 1st Qu.: 3790   1st Qu.: 2.620   1st Qu.: 729.8   1st Qu.:1828  
 Median : 7406   Median : 5.245   Median :1057.5   Median :2134  
 Mean   : 7638   Mean   : 5.490   Mean   : 991.2   Mean   :2304  
 3rd Qu.:10727   3rd Qu.: 7.713   3rd Qu.:1229.5   3rd Qu.:2793  
 Max.   :36019   Max.   :28.030   Max.   :1440.0   Max.   :4900
```
```
## active minutes per category
activity %>%
  select(veryactiveminutes, fairlyactiveminutes, lightlyactiveminutes) %>%
  summary()
```
```
 veryactiveminutes fairlyactiveminutes lightlyactiveminutes
 Min.   :  0.00    Min.   :  0.00      Min.   :  0.0       
 1st Qu.:  0.00    1st Qu.:  0.00      1st Qu.:127.0       
 Median :  4.00    Median :  6.00      Median :199.0       
 Mean   : 21.16    Mean   : 13.56      Mean   :192.8       
 3rd Qu.: 32.00    3rd Qu.: 19.00      3rd Qu.:264.0       
 Max.   :210.00    Max.   :143.00      Max.   :518.0  
```
```
## calories
calories %>%
  select(Calories) %>%
  summary()
```
```
   Calories   
 Min.   :   0  
 1st Qu.:1828  
 Median :2134  
 Mean   :2304  
 3rd Qu.:2793  
 Max.   :4900
```
```
## sleep
sleep %>%
  select(totalsleeprecords, totalminutesasleep, totaltimeinbed) %>%
  summary()
```
```
 totalsleeprecords totalminutesasleep totaltimeinbed 
 Min.   :1.000     Min.   : 58.0      Min.   : 61.0  
 1st Qu.:1.000     1st Qu.:361.0      1st Qu.:403.0  
 Median :1.000     Median :433.0      Median :463.0  
 Mean   :1.119     Mean   :419.5      Mean   :458.6  
 3rd Qu.:1.000     3rd Qu.:490.0      3rd Qu.:526.0  
 Max.   :3.000     Max.   :796.0      Max.   :961.0
```
```
## weight
weight %>%
  select(WeightKg, BMI) %>%
  summary()
```
```
   WeightKg           BMI       
 Min.   : 52.60   Min.   :21.45  
 1st Qu.: 61.40   1st Qu.:23.96  
 Median : 62.50   Median :24.39  
 Mean   : 72.04   Mean   :25.19  
 3rd Qu.: 85.05   3rd Qu.:25.56  
 Max.   :133.50   Max.   :47.54
```
```
## steps
steps %>%
  select(StepTotal) %>%
  summary()
```
```
   StepTotal    
 Min.   :    0  
 1st Qu.: 3790  
 Median : 7406  
 Mean   : 7638  
 3rd Qu.:10727  
 Max.   :36019
```
### Statistical Observations
* The users spend an average of 991.2 minutes sedentary (idle), 16 hours and 52 minutes.

* The average number of steps per day is 7638. The CDC recommends people take 10,000 steps daily.

* The majority of the participants are lightly active.

* The average participant burns 97 calories per hour

* On average, participants sleep for 7 hours.

* The users burnt an average of 2304 calories

### Merging Data
Before visualizing the data, two of the datasets will be merged. I will inner join the activity and sleep datasets on columns ID and date.
```
#inner join sleep and activity

sleep_activity <- merge(sleep, activity, by = c('id', 'date'))
head(sleep_activity)
```
```
         id     date   sleepday totalsleeprecords totalminutesasleep
1 1503960366 04/12/16 2016-04-12                 1                327
2 1503960366 04/13/16 2016-04-13                 2                384
3 1503960366 04/15/16 2016-04-15                 1                412
4 1503960366 04/16/16 2016-04-16                 2                340
5 1503960366 04/17/16 2016-04-17                 1                700
6 1503960366 04/19/16 2016-04-19                 1                304
  totaltimeinbed activitydate totalsteps totaldistance trackerdistance
1            346   2016-04-12      13162          8.50            8.50
2            407   2016-04-13      10735          6.97            6.97
3            442   2016-04-15       9762          6.28            6.28
4            367   2016-04-16      12669          8.16            8.16
5            712   2016-04-17       9705          6.48            6.48
6            320   2016-04-19      15506          9.88            9.88
  loggedactivitiesdistance veryactivedistance moderatelyactivedistance
1                        0               1.88                     0.55
2                        0               1.57                     0.69
3                        0               2.14                     1.26
4                        0               2.71                     0.41
5                        0               3.19                     0.78
6                        0               3.53                     1.32
  lightactivedistance sedentaryactivedistance veryactiveminutes
1                6.06                       0                25
2                4.71                       0                21
3                2.83                       0                29
4                5.04                       0                36
5                2.51                       0                38
6                5.03                       0                50
  fairlyactiveminutes lightlyactiveminutes sedentaryminutes calories
1                  13                  328              728     1985
2                  19                  217              776     1797
3                  34                  209              726     1745
4                  10                  221              773     1863
5                  20                  164              539     1728
6                  31                  264              775     2035
```
## Share
```
ggplot(data = activity, aes(x = totalsteps, y = calories)) + geom_point() + geom_smooth() + labs(title = "Total Steps vs. Calories")

```
![image](https://github.com/ScoopsyDoo/Bellabeat_R_Case_study/assets/113157777/e82f9bab-70ad-4a2b-b5b8-82ece97ec0e3)

Judging from the scatter chart, there appears to be a correlation between the total number of steps taken and calories burned. Participants who take more steps tend to burn more calories.

```
ggplot(data = sleep, aes(x = totalminutesasleep, y = totaltimeinbed)) + geom_jitter() + labs(title = "Total Time Asleep vs Total Time In Bed")

```
![image](https://github.com/ScoopsyDoo/Bellabeat_R_Case_study/assets/113157777/2a8f8f05-edd7-41c0-a2a4-573a5a21604a)

We observed a positive correlation between the total time spent asleep and the total time spent in bed. To help users enhance their sleep quality, Bellabeat should consider allowing them to customize their sleep schedules for consistency.

```
ggplot(data = sleep_activity, mapping = aes(x = sedentaryminutes, y = totalminutesasleep)) +
  geom_jitter() + labs(title= "Sleep Duration and Sedentary Time")
```
![image](https://github.com/ScoopsyDoo/Bellabeat_R_Case_study/assets/113157777/2cf74a24-6562-4e48-9294-df99155395b1)


By analyzing the chart above, we can observe that there is a negative correlation between SedentaryMinutes and TotalMinutesAsleep. Therefore, participants who are less active tend to get less sleep.

```
cor(sleep_activity$totalminutesasleep,sleep_activity$sedentaryminutes)
```
```
# -0.6010731
```
Let's investigate if our activity levels and sleep affect the day of the week.
```
# aggregate data by day of week to summarize averages
sleep_activity <- mutate(sleep_activity, day = wday(sleepday, label = TRUE))
summarized_activity_sleep <- sleep_activity %>% 
  group_by(day) %>% 
  summarise(AvgDailySteps = mean(totalsteps),
            AvgAsleepMinutes = mean(totalminutesasleep),
            AvgAwakeTimeInBed = mean(totaltimeinbed), 
            AvgSedentaryMinutes = mean(sedentaryminutes),
            AvgLightlyActiveMinutes = mean(lightlyactiveminutes),
            AvgFairlyActiveMinutes = mean(fairlyactiveminutes),
            AvgVeryActiveMinutes = mean(veryactiveminutes), 
            AvgCalories = mean(calories))
head(summarized_activity_sleep)
```
```
A tibble: 6 × 9
  day   AvgDailySteps AvgAsleepMinutes AvgAwakeTimeInBed AvgSedentaryMinutes AvgLightlyActiveMinutes
  <ord>         <dbl>            <dbl>             <dbl>               <dbl>                   <dbl>
1 Sun           7298.             453.              504.                688.                    200.
2 Mon           9273.             420.              457.                718.                    222.
3 Tue           9183.             405.              443.                740.                    217.
4 Wed           8023.             435.              470.                714.                    208.
5 Thu           8184.             401.              435.                698.                    203.
6 Fri           7901.             405.              445.                743.                    223.
# ℹ 3 more variables: AvgFairlyActiveMinutes <dbl>, AvgVeryActiveMinutes <dbl>, AvgCalories <dbl>
```
![image](https://github.com/ScoopsyDoo/Bellabeat_R_Case_study/assets/113157777/90de5fb3-a90d-4a95-b21c-b6d42786608a)

The bar graph above shows us that participants are most active on **Saturday**
and least active on **Sunday**.

# ACT

### Insights Summary:

To provide a brief overview of the company's history: Bellabeat manufactures high-tech products focusing on women's health. By gathering data on activity, sleep, stress, and reproductive health, Bellabeat has empowered women with information about their health and habits. Since its establishment in 2013, Bellabeat has experienced rapid growth and has quickly established itself as a technology-driven wellness company for women.

After analyzing data from the Fitbit Fitness Tracker, I have developed recommendations for a Bellabeat marketing strategy based on trends in smart device usage.
 
 1. As stated previously, the average number of daily steps is 7,638. This is lower than what the CDC recommends. According to the CDC's official publication (https://www.cdc.gov/pcd/issues/2016/pdf/16_0111.pdf), 8,000 steps per day was associated with a 51% lower risk for all-cause mortality (or death from all causes). Taking 12,000 steps daily was associated with a 65% lower risk than taking 4,000 steps. One thing Bellabeat can do is suggest that users take at least 8,000 steps per day and explain the benefits that come with it.

 2. The majority of participants are lightly active. Bellabeat should offer a progression system in the app to encourage participants to become at least reasonably active.

 3. Set daily reminders in the Bellabeat app. Notifications can remind users to log their data and encourage healthy habits and exercise.
 
 4. If users want to improve the quality of their sleep, Bellabeat should consider using app notifications to remind users to get enough rest and recommend reducing sedentary time.

 5. Participants are most active on Saturdays. Bellabeat can use this knowledge to remind users to go for a walk or jog that day. Participants are the least active on Sundays. Bellabeat can use this to motivate users to go out and continue exercising on Sundays.

 6. To ensure that our data is up-to-date, we suggest conducting a new survey and inviting at least 100 participants to share their health and habits on social media platforms such as Twitter, Instagram, Facebook, etc. The survey will focus on females because Bellabeat products are designed for women. This will allow us to gather current data and analyze the latest trends in users' health and habits.
 
 7. Bellabeat could offer membership discounts to its customers based on the number of calories they burn through exercise. The Bellabeat app could track the amount of calories the user burns, which is then used to determine the discount percentage. The more calories customers burn, the more significant discount they will receive. By redeeming these discounts, customers can unlock more features to personalize their lifestyle design within the app. This incentive could motivate customers to exercise more to earn discounts and get the most out of the app.

# THANK YOU!



