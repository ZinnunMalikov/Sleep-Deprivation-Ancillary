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
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
```

- Afterward, the raw data file, named "2015data.csv", was converted to a pandas dataframe
```python
#Convert raw data to a usable dataframe array
df_sleep = pd.read_csv(io.BytesIO(uploaded['2015data.csv']))
array_sleep = df_sleep.to_numpy()
```
## 2.3 Basic Functions
- Before the data analysis, two mathematical functions, `stand_dev(inp)` and `reg(inp1, inp2)`, and one debugging function, `isNaN(ni)`, were defined. The function `stand_dev(inp)` takes a list input and returns the sample standrad deviation. The function `reg(inp1, inp2)` takes two list inputs (x and y) and returns the list `std1, std2, r, A, B`, where `std1` is the sample standard deviation of the list input for inp1, `std2` is the sample standard deviation of the list input for inp2, and `r`, `A`, and `B` are the regression constants. Finally, the function `isNaN(ni)` is used to detect NaN outputs, which indicate nonresponse or an empty value.
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

#Data Correction
def isNaN(ni):
  return ni != ni
```



For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/ZinnunMalikov/Sleep-Deprivation-Ancillary/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
