---
layout:     post
title:      Sleep Research Data Analysis with Python 
subtitle:   project ancillary for lit review .
date:       2022-5-15
author:     Zinnun Malikov
header-img: 
catalog: true

tags:

    - Python
    - Jupyter
    - Data Analysis
    - Sleep Deprivation
---

# 1. Background Information

## 1.1 Situating the Issue
- Sleep Deprivation is a serious issue that affects adolescents (aged 14-18). Sleep deprivation has repeatedly been shown to “increase the likelihood myriad negative consequences, including an inability to concentrate, poor grades, drowsy-driving incidents, anxiety, depression, thoughts of suicide and even suicide attempts” (Richter, 2015, pg. 1). This project aims to gather insights from data on sleep collected in a 2015 poll.

## 1.2 Data source
- National Sleep Foundation www.sleepfoundation.org
- 2015 Sleep in America Poll

### 1.3 Data Information
- The National Sleep Foundation counducted an online survey study regarding the sleep of Americans in December 2014 and received responses from 1,029 adults aged 18 years or older. The survey asked participants about their sleep quality, time spent in bed, estimate of actual sleep obtained, etc.

# 2. Introduction for Data Analysis
## 2.1 Raw Data Variables
- Names of variables in the csv file titled "2015data.csv" are listed below:
1. `Q10_a`: sleep quality, 5-point Scale (1: very poor, 2: poor, 3: fair, 4: good, 5: very good)
2. `DOV_WEEKTIME`: average minutes spent in bed per day on weekdays
3. `DOV_WEEKEND`: average minutes spent in bed per day on weekends
4. `DOV_TOTALWEEK`: estimate of actual sleep obtained per day on weekdays (in minutes)
5. `DOV_TOTALWEEK`: estimate of actual sleep obtained per day on weekendss (in minutes)
6. `Q15_c`: how often does light (from either inside or outside) make it more difficult to get a good night's sleep, 5-point Likert Scale (1: never, 2: rarely, 3: sometimes, 4: often, 5: always)

## 2.2 Python Imports 
- The following code was used to import necessary packages, modules, or libraries:
```python
import io
import math
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
```

- Afterward, the raw data file, named "2015data.csv", was uploaded and converted to a pandas dataframe
```python
#Upload File
from google.colab import files
uploaded = files.upload()
#File Name: 2015data.csv

#Convert raw data to a usable dataframe and numpy array
df_sleep = pd.read_csv(io.BytesIO(uploaded['2015data.csv']))
array_sleep = df_sleep.to_numpy()
```
## 2.3 Basic Functions
- Before the data analysis, three mathematical functions, `stand_dev(inp)`, `reg(inp1, inp2)`, and `five_sum(inp_li)`, and one debugging function, `isNaN(ni)`, were defined. The function `stand_dev(inp)` takes a list input and returns the sample standrad deviation. The function `reg(inp1, inp2)` takes two list inputs (x and y) and returns the list `std1, std2, r, A, B`, where `std1` is the sample standard deviation of the list input for inp1, `std2` is the sample standard deviation of the list input for inp2, and `r`, `A`, and `B` are the regression constants. Additionally, the function`five_sum(inp_li)` takes a list input and returns the five numebr summary. Finally, the function `isNaN(ni)` is used to detect NaN outputs, which indicate nonresponse or an empty value.
```python
#Sample Standard Deviation
def stan_dev(inp):
  mean = sum(inp)/len(inp)
  vari = 0
  
  for a in range(len(inp)):
    vari = vari + (1/(len(inp)-1))*(inp[a]-mean)*(inp[a]-mean)
    
  return(vari**0.5)

#Regression Function
def reg(inp1, inp2):
  mean1 = sum(inp1)/len(inp1)
  mean2 = sum(inp2)/len(inp2)
  vari1 = 0
  vari2 = 0
  
  for a in range(len(inp1)):
    vari1 = vari1 + (1/(len(inp1)-1))*(inp1[a]-mean1)*(inp1[a]-mean1)
    
  for a in range(len(inp1)):
    vari2 = vari2 + (1/(len(inp2)-1))*(inp2[a]-mean2)*(inp2[a]-mean2)
    
  std1 = vari1**0.5
  std2 = vari2**0.5
  
  r = 0
  
  for a in range(len(inp1)):
    r = r + (1/(len(inp1)-1))*(inp1[a]-mean1)*(inp2[a]-mean2)/(std1*std2)
    
  B = r*std2/std1
  A = mean2-mean1*B
  
  return [std1, std2, r, A, B]

#Five Number Summary
def five_sum(inp_li):
  inp_li.sort()
  dnum = len(inp_li)

  if len(inp_li) % 2 == 1:
      median = inp_li[int((dnum + 1)/2 - 1)]
      lh = inp_li[0:int((dnum + 1)/2 - 1)]
      uh = inp_li[int((dnum+1)/2):]
  else:
      median = (inp_li[int(dnum/2) - 1] + inp_li[int(dnum/2)])/2
      lh = inp_li[0: int(dnum/2)]
      uh = inp_li[int(dnum/2):]


  if len(lh) % 2 == 1:
      q1 = lh[int((len(lh) + 1)/2 - 1)]
  else:
      q1 = (lh[int(len(lh)/2 - 1)] + lh[int(len(lh)/2)])/2

  if len(uh) % 2 == 1:
      q3 = uh[int((len(uh) + 1)/2 - 1)]
  else:
      q3 = (uh[int(len(uh)/2 - 1)] + uh[int(len(uh)/2)])/2

  iqr = q3 - q1
  return([min(inp_li), q1, median, q3, max(inp_li)])

#Data Correction
def isNaN(ni):
  return ni != ni
```

## 2.4 Data Construction and Correction
- Parts of the numpy array were then stored to several variables. The variables `avg_weekdays` and `avg_weekends` gave the total estimated actual sleep times (minutes) per day during the weekdays and weekends, respectively, and the variable `avg_total` averaged these values and converted them to hours. Afterward, the variable `sleep_quality` was created to store the responses of individuals when asked about their sleep quality (1-5 point scale), and the variable `light_exp` was created to store the responses of individuals when asked how frequently external light exposure disturbed their sleep (1-5 point scale). Finally, the variables `bed_week` and `bed_weekend` gave the total estimated times spent in bed (minutes) per day during the weekdays and weekends, respectively, and the variable `avg_bed` averaged these values and converted them to hours.
```python
avg_weekdays = array_sleep[:, 39]
avg_weekends = array_sleep[:, 40]
avg_total = list(np.add(avg_weekdays*5/(60*7), avg_weekends*2/(60*7)))
sleep_quality = array_sleep[:, 61]
light_exp = array_sleep[:, 98]
bed_week = array_sleep[:, 25]
bed_weekend = array_sleep[:, 33]
avg_bed = list(np.add(bed_week*5/(60*7), bed_weekend*2/(60*7)))
```
- However, some issues arose. There were some instances in which values were empty due to nonresponse. For example, some of the numerical values returned NaN values and the categorical responses for the five point scale were recorded as a -1. To account for this, the `isNaN(ni)` function was used to detect empty values and replace them with the mean value of that variable for the sample. Values of -1 were excluded, however.
```python
#Correction for avg_weekdays, avg_weekends, avg_total
for a in range(len(avg_weekdays)):
  if isNaN(avg_weekdays[a]) == True:
    avg_weekdays[a] = 411.45
  if isNaN(avg_weekends[a]) == True:
    avg_weekends[a] = 449.9
    
avg_total = list(np.add(avg_weekdays*5/(60*7), avg_weekends*2/(60*7)))

#Correction for bed_week, bed_weeknd, avg_bed
for a in range(len(bed_week)):
  if isNaN(bed_week[a]) == True:
    bed_week[a] = 475.3
  if isNaN(bed_weekend[a]) == True:
    bed_weekend[a] = 524.25

avg_bed = list(np.add(bed_week*5/(60*7), bed_weekend*2/(60*7)))
```

# 3. Data Analysis
## 3.1 Distribution of Average Actual Sleep Times
- The first generated plot displayed the distribution of average actual sleep times (hrs) for the 1029 surveyed individuals. Additionally, the five number summary (min, q1, median, q3, max) was generated.
```python
#Make data
x = avg_total

#Limits
xlow = min(avg_total)
xhigh = max(avg_total)

#Plot:
fig, ax = plt.subplots()

ax.hist(x, bins=32, linewidth=0.5, edgecolor="white")

ax.set(xlim=(0, xhigh))

plt.title("Distrbution of the Average Sleep Times of 1029 Individuals (2015)")
plt.xlabel("Hours")
plt.ylabel("Frequency")
print(five_sum(avg_total))

plt.show()
```
    [1.5357142857142858, 6.285714285714286, 7.0, 7.928571428571429, 12.75]

![img1](https://user-images.githubusercontent.com/97144011/168615643-21c49d95-154b-4727-b23b-8cc68d178892.png)



