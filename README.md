# Google Data Analytics Case Study: Bellabeat
### Author: Audrey Sung
### Date: May 2022
#

Below is my case study for the Google Data Analytics Capstone organized by the six steps of the data analysis process: **ask**, **prepare**, **process**, **analyze**, **share**, and **act**.

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
