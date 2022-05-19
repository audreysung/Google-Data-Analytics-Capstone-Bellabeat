# Google Data Analytics Case Study: Bellabeat
### Author: Audrey Sung
### Date: May 2022
#

Below is my case study for the Google Data Analytics Capstone organized by the six steps of the data analysis process: 
### [Ask](#1-ask)
### [Prepare](#2-prepare)
### [Process](#3-process)
### [Analyze](#4-analyze)
### [Share](#5-share)
### [Act](#6-act)

## Scenario
Founded in 2013 by Urška Sršen and Sando Mur, Bellabeat is a high-tech company that manufactures health-focused smart products for women. For example, the Bellabeat app provides important health data related to activity, sleep, stress, menstrual cycle, and mindfulness habits. This app can connect to various smart wellness products including bracelets, necklaces, clips, watches, and water bottles. With its rapid growth, Bellabeat has a promising future to expand to the global smart device market. Our marketing analyst team has been asked to analyze smart device fitness data to help unlock new growth opportunities for Bellabeat. The insights we discover will then help guide marketing strategy for the company.

## 1. Ask
**Business Task:** Analyze trends in FitBit usage to guide Bellabeat’s marketing strategy for their smart devices

**Stakeholders:**
- Urška Sršen: Cofounder and Chief Creative Officer
- Sando Mur: Cofounder and key member of Bellabeat executive team

## 2. Prepare
**FitBit Fitness Tracker Data**
- Source: https://www.kaggle.com/datasets/arashnic/fitbit
- Stored in Kaggle and made available through Mobius
- Licensed under CC0: Public Domain (the creator has made this dataset public for everyone to use)
- Generated by respondants to a distributed survey via Amazon Mechanical Turk
- Involved 30 FitBit users 
- Occured between 3/12/2016 - 5/12/2016 
- Included personal data such as minute-level output for physical activity, heart rate, and sleep monitoring
- Made up of 18 CSV files organized in long format.

**3 Main Tables for Analysis in My Case Study**
- dailyActivity_merged -> Daily Activity
- sleepDay_merged -> Sleep
- weightLogInfo_merged -> Weight

**Assessing Credibility with ROCCC**
- Reliable: Low since we only have data for 30 individuals with unknown demographics.
- Original: Low since the dataset was collected by a third-party source.
- Comprehensive: Medium since dataset contains many variables including daily activity, heart rate, calories, steps, intensities, metabolic equivalents, sleep, and weight but does not have participant demographics.
- Current: Low since dataset was collected 6 years ago.
- Cited:Low since dataset is from a publci source.

**Limitations**
- Only 30 people participated in this dataset, which brings up the concern of sampling bias. In addition, demographics like gender or age are unknown.
- The timing of this dataset is not only old but also limited because it occurred in 2016 and happened for a span of 2 months.
- In Google Sheets, I checked the dataset for unique user IDs: 33 users for daily activity, daily calories, daily intensities, and daily steps; 24 users for sleep; and 8 users for weight. These numbers differ from 30 respondents involved in this survey.

## 3. Process
For the Process phase, I decided to use R because it will be a helpful tool to clean and analyze my data. Below, I described how I cleaned the following datasets: daily activity, sleep, and weight.

**Importing Datasets**
```
daily_activity <- read_csv("Downloads/Fitabase Data 4.12.16-5.12.16/dailyActivity_merged.csv")
daily_calories <- read_csv("Downloads/Fitabase Data 4.12.16-5.12.16/dailyCalories_merged.csv")
daily_intensities <- read_csv("Downloads/Fitabase Data 4.12.16-5.12.16/dailyIntensities_merged.csv")
daily_steps <- read_csv("Downloads/Fitabase Data 4.12.16-5.12.16/dailySteps_merged.csv")
sleep_day <- read_csv("Downloads/Fitabase Data 4.12.16-5.12.16/sleepDay_merged.csv")
weight_log <- read_csv("Downloads/Fitabase Data 4.12.16-5.12.16/weightLogInfo_merged.csv")
```

**daily_activity**
- Previewed dataset
- Checked for missing values, duplicates
- Renamed ActivityDate to Date
- Changed Date column from character to date type
- Created new column with TotalActiveMinutes

```
# Preview
head(daily_activity)
```
![image](https://user-images.githubusercontent.com/105669325/169112648-634f4aac-44a5-4453-bcde-83aac56776ee.png)
```
# Count unique respondants
n_distinct(daily_activity$Id)
[1] 33

# Check for missing values
sum(is.na(daily_activity))
[1] 0

# Check for duplicates 
sum(duplicated(daily_activity))
[1] 0

# Clean dataset
clean_names(daily_activity)
```
![image](https://user-images.githubusercontent.com/105669325/169121995-016e72cb-b072-4c76-9f75-1045f4ae7193.png)
```
# Rename ActivityDate to Date and change Date colum from character to date type
daily_activity <- daily_activity %>%
  rename(Date = ActivityDate) %>%
  mutate(Date = as_date(Date, format = "%m/%d/%Y"))

# Create new column with TotalActiveMinutes
daily_activity_updated <- daily_activity %>%
  mutate (TotalActiveMinutes = VeryActiveMinutes + FairlyActiveMinutes + LightlyActiveMinutes)
  
# Confirm new table
head(daily_activity_updated)
```
![image](https://user-images.githubusercontent.com/105669325/169126930-ccb72be2-f221-4323-850b-f329ac2d9e73.png)

**sleep_day**
- Previewed dataset
- Checked for missing values, duplicates
- Removed duplicates and NA
- Renamed SleepDay to Date
- Changed Date column from character to date type

```
# Preview
head(sleep_day)
```
![image](https://user-images.githubusercontent.com/105669325/169130984-bf1bd1ce-d278-449a-9ea6-2bdff2efe551.png)
```
# Count unique respondants
n_distinct(sleep_day$Id)
[1] 24

# Check for missing values
sum(is.na(sleep_day))
[1] 0

# Check for duplicates 
sum(duplicated(sleep_day))
[1] 3

#Remove duplicates and NA
sleep_day <- sleep_day %>% 
  distinct() %>% 
  drop_na()
  
# Check duplicates were removed
sum(duplicated(sleep_day))
[1] 0

# Clean dataset
clean_names(sleep_day)
```
![image](https://user-images.githubusercontent.com/105669325/169135027-feb8347a-263f-473e-aeac-4afa6e88e6c0.png)```
```
# Rename SleepDay to Date and change Date colum from character to date type
sleep_day <- sleep_day %>%
  rename(Date = SleepDay) %>%
  mutate(Date = as_date(Date,format ="%m/%d/%Y"))
 
# Confirm new table
head(sleep_day)
```
![image](https://user-images.githubusercontent.com/105669325/169136179-f86c538d-f99e-4888-9d72-0959a6ac814d.png)

**weight_log**
- Previewed dataset
- Checked for missing values, duplicates
- Changed Date column from character to date type

```
# Preview
head(weight_log)
```
![image](https://user-images.githubusercontent.com/105669325/169146995-ac799574-01db-45eb-abf0-331bddffb4bc.png)

```
# Count unique respondants
n_distinct(weight_log$Id)
[1] 8

# Check for missing values
sum(is.na(weight_log$WeightPounds))
[1] 0

# Check for duplicates 
sum(duplicated(weight_log))
[1] 3

# Clean dataset
clean_names(weight_log)
```
![image](https://user-images.githubusercontent.com/105669325/169147289-2d5fa9ce-5ce8-4d59-b49a-17fa93024d0d.png)

```
# Change Date colum from character to date type
weight_log <- weight_log %>%
  rename(Date2 = Date) %>%
  mutate(Date2 = as_date(Date2, format = "%m/%d/%Y"))
 
# Confirm new table
head(weight_log)
```
![image](https://user-images.githubusercontent.com/105669325/169148427-e2f2cd58-d199-4710-8e20-ed523c35d961.png)

## 4. Analyze
Below are my summary statistics that I calculated with the help of R.

**Number of Observations**
```
nrow(daily_activity_updated)
[1] 940

nrow(sleep_day)
[1] 410

nrow(weight_log)
[1] 67
```

**Summary Statistics for Daily Activity**
```
daily_activity_updated %>%  
  select(TotalSteps,
         TotalDistance,
         SedentaryMinutes,
         TotalActiveMinutes) %>%
  summary()
  ```
  ![image](https://user-images.githubusercontent.com/105669325/169151350-06df9eb5-d20b-4d36-9077-171c0d6ac876.png)
  

In this table, participants walked from 0 steps to 36,019 steps with an average of 7,638 steps daily. On average, people were sedentary for 991.2 minutes (approximately 17 hours) while people were active for 227.5 minutes (approximately 4 hours). 


**Summary Statistics for Sleep**
```
sleep_day %>%  
  select(TotalSleepRecords,
         TotalMinutesAsleep,
         TotalTimeInBed) %>%
  summary()
  ```
  ![image](https://user-images.githubusercontent.com/105669325/169154088-5c5cf6e4-7cb7-441d-b17b-1cc6ba908de1.png)
  
  
In this table, participants were asleep for a minimum of 58 minutes (approximately 1 hour) to a maximum of 796 minutes (approximately 13 hours) with an average of 419.2 minutes (approximately 7 hours). They were also in bed for a minimum of 61 minutes (approximately 1 hour) to a maximum of 961 minutes (approximately 16 hours) with an average of 458.5 minutes (approximately 8 hours).

**Summary Statistics for Weight**
```
weight_log %>%  
  select(WeightPounds,
         BMI) %>%
  summary()
```
![image](https://user-images.githubusercontent.com/105669325/169155173-cbe5124d-8cbf-4e29-9f58-4da72e0da1f9.png)


In this table, respondents weighed from a minimum of 116 pounds to a maximum of 294.3 pounds with an average of 158.8 pounds. They also had BMIs ranging from 21.45 to 47.54 with an average of 25.19.


**Conclusions**


Based on this dataset, participants walked an average of 7,638 steps daily. This is below the recommended threshold of 10,000 steps as noted in this [CDC article](https://www.cdc.gov/diabetes/prevention/pdf/postcurriculum_session8.pdf).

However, participants on average were getting the recommonded amount of sleep with an average of approximately 7 hours. For adults, the [CDC recommends at least 7 hours of sleep](https://www.cdc.gov/sleep/about_sleep/how_much_sleep.html).

According to this [CDC article](https://www.cdc.gov/healthyweight/assessing/bmi/adult_bmi/index.html#InterpretedAdults), a BMI below 18.5 is underweight, 18.5 - 24.9 is healthy weight, 25.0 - 29.9 is overweight, and 30.0 and above is obese. Of note, these values apply to adults 20 years old and order. One limitation of our FitBit dataset is the lack of knowledge of respondent demographics. However, if we assume that the respondents in our data are 20 years old and order, they are on average overweight (25.19). Interestingly, the BMI values range from healthy weight (21.45) to obese (47.54). 

## 5. Share
Below are my data visualizations.

**What is the relationship between steps taken in a day and sedentary minutes? How is the number of calories burnt affected by this relationship?**
```
ggplot(data = daily_activity_updated, aes(x = TotalSteps, y = SedentaryMinutes, color = Calories)) +
      geom_point() +
      labs(title = "Total Steps vs Sedentary Minutes", x = "Total Steps", y = "Sedentary Minutes")
```


![Rplot](https://user-images.githubusercontent.com/105669325/169315941-64eee83b-681d-4e4e-ad2b-b47ef0d2ff14.png)


Based on this data visualization, there appears to be no correlation between the number of steps taken in a day and sedentary minutes since the data seems to be clustered around each other. However, this data visualization shows that total steps are positively correlated with calories burnt: the more steps people walk, the more calories they burn. 

**What is the relationship between steps taken in a day and total active minutes? How is the number of calories burnt affected by this relationship?**
```
ggplot(data = daily_activity_updated, aes(x = TotalSteps, y = TotalActiveMinutes, color = Calories)) +
  geom_point() +
  geom_smooth() +
  labs(title = "Total Steps vs Total Active Minutes", x = "Total Steps", y = "Total Active Minutes")
```


![Rplot](https://user-images.githubusercontent.com/105669325/169321159-cfc11e8e-07cb-4da8-8dbb-bf1eb4a71351.png)

Based on this data visualization, there is a positive linear relationship between the number of steps taken in a day and total active minutes. While total steps are positively correlated with calories burnt, the correlation between total active minutes and calories burnt is not as clear surprisingly. It may be due to the fact that I combined lightly active, fairly active, and very active minutes to be total active minutes.  


**What is the relationship between minutes asleep and time in bed?**
```
ggplot(data = sleep_day, aes(x = TotalMinutesAsleep, y = TotalTimeInBed)) +
      geom_point() +
      geom_smooth() +
      labs(title = "Total Minutes Asleep vs Total Time In Bed", x = "Total Minutes Asleep", y = "Total Time In Bed")
```


![Rplot01](https://user-images.githubusercontent.com/105669325/169396319-3c061794-5624-48e6-9717-94afe6e106ef.png)


This data visualization shows a positive linear relationship between minutes asleep and time in bed. This makes sense because people sleep in their beds; therefore, the more time spent sleeping correlates with more time spent in bed. 


**What is the percentage of activity in minutes that people spend on daily?**
```
# Calculating sum of total minutes
sum(daily_activity_updated$VeryActiveMinutes, daily_activity_updated$FairlyActiveMinutes, daily_activity_updated$LightlyActiveMinutes, daily_activity_updated$SedentaryMinutes)
[1] 1145628

# Creating pie chart
Activity <- daily_activity_updated %>%
  summarise(Sum_VAM = sum(daily_activity_updated$VeryActiveMinutes/1145628*100), 
            Sum_FAM = sum(daily_activity_updated$FairlyActiveMinutes/1145628*100), 
            Sum_LAM = sum(daily_activity_updated$LightlyActiveMinutes/1145628*100), 
            Sum_SEM = sum(daily_activity_updated$SedentaryMinutes/1145628*100),
            Sum_TOTAL=sum(daily_activity_updated$VeryActiveMinutes+daily_activity_updated$FairlyActiveMinutes+daily_activity_updated$LightlyActiveMinutes+daily_activity_updated$SedentaryMinutes)) %>% 
  round(digits = 2)

slices <- c(Activity$Sum_VAM, Activity$Sum_FAM, Activity$Sum_LAM, Activity$Sum_SEM)
lbls <- c("Very Active", "Fairly Active", "Lightly Active", "Sedentary")
pie(slices,
    labels=paste(lbls, slices, sep=" ", "%"),
    col = rainbow(6),
    main="Percentage Of Daily Activity In Minutes")
```


![Rplot](https://user-images.githubusercontent.com/105669325/169399345-e50cfeba-e700-494f-ba75-d8dec79eaa11.png)


Approximately 81% of the respondents are sedentary, 15.82% are lightly active, 1.11% are fairly active, and 1.74% are very active. The majority of FitBit users track their sedentary activities rather than their active activities. These users do not track their fitness consistently especially since the minority (approximately 1%) consists of fairly active and very active activities. According to the [Physical Activity Guidelines for Americans](https://www.cdc.gov/physicalactivity/walking/index.htm), adults are recommended to get at least 150 minutes of moderate-intensity aerobic physical activity or 75 minutes of vigorous-intensity physical activity, or an equivalent combination each week. These numbers would convert to at least 21.4 minutes of moderate-intensity aerobic physical activity daily or 10.7 minutes of vigorous-intensity physical activity daily. Based on the calculation below, 30 users met this recommendation of physical activity.

```
active_people <- daily_activity_updated %>%
  filter(FairlyActiveMinutes >= 21.4 | VeryActiveMinutes>=10.7) %>% 
  group_by(Id) %>% 
  count(Id) 
n_distinct(active_people)
[1] 30
```

## 6. Act
**Conclusions**

final conclusion, is there additional data you could use to expand on your findings?
business task: Analyze trends in FitBit usage to guide Bellabeat’s marketing strategy for their smart devices

**Recommendations**

how could your team and business apply your insights?

**Next Steps**

what next steps would you or your stakeholders take based on your findings?
