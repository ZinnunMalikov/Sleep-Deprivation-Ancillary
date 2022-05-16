---
title:      Sleep Research Data Analysis with Python 
subtitle:   project ancillary for lit review .
base_url: "zinnunmalikov"
url: ""
date:       2022-5-15
author:     Zinnun Malikov

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

# 3. Data Analysis of Actual Sleep Times
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

## 3.2 Pie Chart Representation
- To better visualize the data from the histogram above, two pie charts that divided individuals into groups by sleep times were created. The first chart included the following labels: '<6 hours', '6-7 hours', '7-8 hours', '8+ hours'. The second chart included the following labels: '<7 hours', '7+ hours'. Note thta the labels were created from general sleep time suggestions.
```python
#Percent of those who slept: (<6), (6-7), (7, 8), (8+) hours
p1 = 0
p2 = 0
p3 = 0
p4 = 0
for num in avg_total:
  if num < 6:
    p1 += 1
  elif num < 7:
    p2 += 1
  elif num < 8:
    p3 += 1
  else:
    p4 += 1

#First Pie Chart
labels = ["<6 hours", "6-7 hours", "7-8 hours", "8+ hours"]
sizes = [p1, p2, p3, p4]
explode = [0, 0, 0, 0]

plt.pie(sizes, autopct='%1.0f%%', explode=explode,labels=labels,shadow=True,startangle=90, )
plt.axis("equal")
plt.legend(title="Categorical Distribution of Sleep Time from 2015 Survey", loc = 'best')
plt.subplots_adjust(left=0.2, bottom=0.1, right=2.5)
plt.show()
```
![img2](https://user-images.githubusercontent.com/97144011/168616669-7c88274e-8532-436e-8641-67fe58225629.png)
Note that the groups do not sum to 100% due to rounding.

```python
#Second Pie Chart
labels = ["<7 hours", "7+ hours"]
sizes = [p1+p2, p3+p4]
explode=[0,0]

plt.pie(sizes, autopct='%1.0f%%', explode=explode,labels=labels,shadow=True,startangle=90, )
plt.axis("equal")
plt.legend(title="Categorical Distribution of Sleep Times from 2015 Survey", loc = 'best')
plt.subplots_adjust(left=0.2, bottom=0.1, right=2.5)
plt.show()
```
![img3](https://user-images.githubusercontent.com/97144011/168616788-134e8f89-625f-4792-b443-f5e6f58449a3.png)

- Based on the data, it is evident that approximately 40% of the surveyed individuals slept for less than seven hours. This figure is concerning, as it highlights the presence of sleep deprivation in the United States. 

# 4. Data Analysis of Sleep Quality and Light Exposure 
## 4.1 Sleep Times (hrs) vs. Sleep Quality (1-5)
- We next considered the relationship between sleep quality and sleep times.
```python
#Relationship between sleep duration and sleep quality
sleep_quality = array_sleep[:, 61]
sleep_quality = list(sleep_quality)

avg1 = []
avg2 = []
avg3 = []
avg4 = []
avg5 = []

for a in range(len(sleep_quality)):
  if sleep_quality[a] == 1:
    avg1.append(avg_total[a])
  if sleep_quality[a] == 2:
    avg2.append(avg_total[a])
  if sleep_quality[a] == 3:
    avg3.append(avg_total[a])
  if sleep_quality[a] == 4:
    avg4.append(avg_total[a])
  if sleep_quality[a] == 5:
    avg5.append(avg_total[a])

#Quantitative v. Categorical

# make data:
D = np.array([avg1, avg2, avg3, avg4, avg5], dtype=object)
labels = ["1", "2", "3", "4", "5"]

# plot
fig, ax = plt.subplots()
VP = ax.boxplot(D, labels = labels, positions=[2, 4, 6, 8, 10], widths=1.5, patch_artist=True,
                showmeans=False, showfliers=False,
                medianprops={"color": "white", "linewidth": 0.5},
                boxprops={"facecolor": "C1", "edgecolor": "white",
                          "linewidth": 0.5},
                whiskerprops={"color": "C1", "linewidth": 1.5},
                capprops={"color": "C1", "linewidth": 1.5})
plt.xlabel("Sleep Quality Score (1-5)")
plt.ylabel("Sleep Time (hrs)")
plt.title("Distribution of Sleep Duration (hrs) for Varying Sleep Quality Scores (1-5) of 1029 Individuals (2015)")

plt.show()
```
![img4](https://user-images.githubusercontent.com/97144011/168618105-79642283-d142-4490-a3dc-6705258a18ee.png)

- To further investigate this relationship, we conducted a chi-squared test for independence to determine if there was convincing evidence at the 5% level for a difference in distributions of sleep times for varying sleep quality scores. The following code creates the observed and expected counts tables.
```python
#Chi Squared Test for Independence

#Observed Counts
r1c1 = []
r1c2 = []
r1c3 = []
r1c4 = []
r1c5 = []

r2c1 = []
r2c2 = []
r2c3 = []
r2c4 = []
r2c5 = []

r3c1 = []
r3c2 = []
r3c3 = []
r3c4 = []
r3c5 = []

r4c1 = []
r4c2 = []
r4c3 = []
r4c4 = []
r4c5 = []

row1 = [r1c1, r1c2, r1c3, r1c4, r1c5]
row2 = [r2c1, r2c2, r2c3, r2c4, r2c5]
row3 = [r3c1, r3c2, r3c3, r3c4, r3c5]
row4 = [r4c1, r4c2, r4c3, r4c4, r4c5]

for a in range(len(avg_total)):
  for b in range(len(row1)):
    if avg_total[a] < 6 and sleep_quality[a] == b+1:
      row1[b].append(a)
    if 6 <= avg_total[a] < 7 and sleep_quality[a] == b+1:
      row2[b].append(a)
    if 7 <= avg_total[a] < 8 and sleep_quality[a] == b+1:
      row3[b].append(a)
    if 8 <= avg_total[a] and sleep_quality[a] == b+1:
      row4[b].append(a)

for a in range(len(row1)):
  row1[a] = len(row1[a])
  row2[a] = len(row2[a])
  row3[a] = len(row3[a])
  row4[a] = len(row4[a])

two_way_tab = np.array([row1, row2, row3, row4])

row_sums = np.array([[sum(row1)], [sum(row2)], [sum(row3)], [sum(row4)], [1029]])

column_sums = np.array([np.add(np.add(np.add(np.array(row1), np.array(row2)), np.array(row3)), np.array(row4))])
two_way_tab = np.append(two_way_tab, column_sums, axis=0)
two_way_tab = np.append(two_way_tab, row_sums, axis=1)

two_way_df = pd.DataFrame(two_way_tab)
two_way_df.columns = ["1", "2", "3", "4", "5", "Total"]
two_way_df.index = ["<6 hrs", "6-7 hrs", "7-8 hrs", "8+ hours", "Total"]

print("Observed Counts for Relative Frequency of Sleep Quality (1-5) vs. Sleep Time (hrs)")
print(two_way_df)

#Expected Counts
two_way_tab2 = np.empty((4, 5), float)

row_sums_list = [sum(row1), sum(row2), sum(row3), sum(row4)]
column_sums_list = list(two_way_tab[4, 0:5])

for a in range(len(row_sums_list)):
  for b in range(len(column_sums_list)):
    two_way_tab2[a, b] = float(two_way_tab2[a, b])
    two_way_tab2[a, b] = np.around(float(row_sums_list[a]*column_sums_list[b]/1029), 3)

two_way_tab2 = np.append(two_way_tab2, column_sums, axis=0)
two_way_tab2 = np.append(two_way_tab2, row_sums, axis=1)

two_way_dfE = pd.DataFrame(two_way_tab2)
two_way_dfE.columns = ["1", "2", "3", "4", "5", "Total"]
two_way_dfE.index = ["<6 hrs", "6-7 hrs", "7-8 hrs", "8+ hours", "Total"]

print("")
print("Expected Counts for Relative Frequency of Sleep Quality (1-5) vs. Sleep Time (hrs)")
print(two_way_dfE)
```
    Observed Counts for Relative Frequency of Sleep Quality (1-5) vs. Sleep Time (hrs)
               1   2    3    4    5  Total
    <6 hrs    20  39   61   26   11    157
    6-7 hrs    4  24  125   92   13    258
    7-8 hrs    1  20  120  170   53    364
    8+ hours   4   8   52  118   68    250
    Total     29  91  358  406  145   1029

    Expected Counts for Relative Frequency of Sleep Quality (1-5) vs. Sleep Time (hrs)
                   1       2        3        4        5   Total
    <6 hrs     4.425  13.884   54.622   61.946   22.123   157.0
    6-7 hrs    7.271  22.816   89.761  101.796   36.356   258.0
    7-8 hrs   10.259  32.190  126.639  143.619   51.293   364.0
    8+ hours   7.046  22.109   86.978   98.639   35.228   250.0
    Total     29.000  91.000  358.000  406.000  145.000  1029.0

- The following code calculates the chi-squared tets statistic using the two two-way tables above.
```python
#Chi Squared Test Statistic
#Note: One out of the twenty (5%) expected counts are under 5. However, for general purposes, the chi quared test can be continued

row_num, col_num = two_way_tab2.shape

chi_sq_stat = 0
for a in range(row_num - 1):
  for b in range(col_num - 1):
    partial = ((two_way_tab[a, b] - two_way_tab2[a, b])**2)/two_way_tab2[a, b]
    chi_sq_stat += partial

print("The chi-squared statistic for this test is: " + str(chi_sq_stat))
```
    The chi-squared statistic for this test is: 235.6631248649211

- The test statistic of 235.663 with a df = 12 yields a p-value far below 0.05 so we reject Ho. There is convincing evidence at the 5% alpha level that there is an association between sleep quality (1-5) and sleep time (hrs) in individuals like those surveyed.

## 4.2 Sleep Times (hrs) vs. Exposure to External Light (1-5)
- We next investigated the relationship between sleep times (hrs) and exposure to external light (1-5).
```python
#Relationship Between Light Exposure (1-5) and Sleep Time (hrs)
light_exp = array_sleep[:, 98]
light_exp = list(light_exp)

lig1 = []
lig2 = []
lig3 = []
lig4 = []
lig5 = []

for a in range(len(light_exp)):
  if light_exp[a] == 1:
    lig1.append(avg_total[a])
  if light_exp[a] == 2:
    lig2.append(avg_total[a])
  if light_exp[a] == 3:
    lig3.append(avg_total[a])
  if light_exp[a] == 4:
    lig4.append(avg_total[a])
  if light_exp[a] == 5:
    lig5.append(avg_total[a])

#Quantitative v. Categorical

# make data:
D = np.array([lig1, lig2, lig3, lig4, lig5], dtype=object)
labels = ["1", "2", "3", "4", "5"]

# plot
fig, ax = plt.subplots()
VP = ax.boxplot(D, labels = labels, positions=[2, 4, 6, 8, 10], widths=1.5, patch_artist=True,
                showmeans=False, showfliers=False,
                medianprops={"color": "white", "linewidth": 0.5},
                boxprops={"facecolor": "C1", "edgecolor": "white",
                          "linewidth": 0.5},
                whiskerprops={"color": "C1", "linewidth": 1.5},
                capprops={"color": "C1", "linewidth": 1.5})
plt.xlabel("External Light Exposure (1-5)")
plt.ylabel("Sleep Time(hrs)")
plt.title("Distribution of Sleep Duration (hrs) for Varying Exposure to External Light (1-5) of 1029 Individuals (2015)")
plt.show()
```
![img5](https://user-images.githubusercontent.com/97144011/168624946-02bee1a3-8001-4e76-9b0a-1bb6268cedf3.png)
- To further investigate this relationship, we conducted a chi-squared test for independence to determine if there was convincing evidence at the 5% level for a difference in distributions of sleep times for varying sleep quality scores. The following code creates the observed and expected counts tables.

```python
#Chi-Squared Test for Independence
#Observed Counts
for a in range(len(light_exp)):
  if isNaN(light_exp[a]) == True:
    light_exp[a] = 2

r1c1 = []
r1c2 = []
r1c3 = []
r1c4 = []
r1c5 = []

r2c1 = []
r2c2 = []
r2c3 = []
r2c4 = []
r2c5 = []

r3c1 = []
r3c2 = []
r3c3 = []
r3c4 = []
r3c5 = []

r4c1 = []
r4c2 = []
r4c3 = []
r4c4 = []
r4c5 = []

row1 = [r1c1, r1c2, r1c3, r1c4, r1c5]
row2 = [r2c1, r2c2, r2c3, r2c4, r2c5]
row3 = [r3c1, r3c2, r3c3, r3c4, r3c5]
row4 = [r4c1, r4c2, r4c3, r4c4, r4c5]

for a in range(len(avg_total)):
  for b in range(len(row1)):
    if avg_total[a] < 6 and light_exp[a] == b+1:
      row1[b].append(a)
    if 6 <= avg_total[a] < 7 and light_exp[a] == b+1:
      row2[b].append(a)
    if 7 <= avg_total[a] < 8 and light_exp[a] == b+1:
      row3[b].append(a)
    if 8 <= avg_total[a] and light_exp[a] == b+1:
      row4[b].append(a)

for a in range(len(row1)):
  row1[a] = len(row1[a])
  row2[a] = len(row2[a])
  row3[a] = len(row3[a])
  row4[a] = len(row4[a])

two_way_tab = np.array([row1, row2, row3, row4])

row_sums = np.array([[sum(row1)], [sum(row2)], [sum(row3)], [sum(row4)], [1026]])

column_sums = np.array([np.add(np.add(np.add(np.array(row1), np.array(row2)), np.array(row3)), np.array(row4))])
two_way_tab = np.append(two_way_tab, column_sums, axis=0)
two_way_tab = np.append(two_way_tab, row_sums, axis=1)

two_way_df = pd.DataFrame(two_way_tab)
two_way_df.columns = ["1", "2", "3", "4", "5", "Total"]
two_way_df.index = ["<6 hrs", "6-7 hrs", "7-8 hrs", "8+ hours", "Total"]

print("Observed Counts for Relative Frequency of External Light Exposure (1-5) vs. Sleep Time (hrs)")
print(two_way_df)

#Expected Counts
two_way_tab2 = np.empty((4, 5), float)

row_sums_list = [sum(row1), sum(row2), sum(row3), sum(row4)]
column_sums_list = list(two_way_tab[4, 0:5])

for a in range(len(row_sums_list)):
  for b in range(len(column_sums_list)):
    two_way_tab2[a, b] = float(two_way_tab2[a, b])
    two_way_tab2[a, b] = np.around(float(row_sums_list[a]*column_sums_list[b]/1029), 3)

two_way_tab2 = np.append(two_way_tab2, column_sums, axis=0)
two_way_tab2 = np.append(two_way_tab2, row_sums, axis=1)

two_way_dfE = pd.DataFrame(two_way_tab2)
two_way_dfE.columns = ["1", "2", "3", "4", "5", "Total"]
two_way_dfE.index = ["<6 hrs", "6-7 hrs", "7-8 hrs", "8+ hours", "Total"]

print("")
print("Expected Counts for Relative Frequency of Sleep Quality (1-5) vs. Sleep Time (hrs)")
print(two_way_dfE)
```
    Observed Counts for Relative Frequency of External Light Exposure (1-5) vs. Sleep Time (hrs)
                1    2    3   4   5  Total
    <6 hrs     84   44   22   3   4    157
    6-7 hrs   136   75   34   7   6    258
    7-8 hrs   178  119   48  14   4    363
    8+ hours  124   89   32   3   0    248
    Total     522  327  136  27  14   1026

    Expected Counts for Relative Frequency of Sleep Quality (1-5) vs. Sleep Time (hrs)
                    1        2        3       4       5   Total
    <6 hrs     79.877   50.038   20.811   4.132   2.142   157.0
    6-7 hrs   131.263   82.228   34.199   6.789   3.520   258.0
    7-8 hrs   184.684  115.693   48.117   9.553   4.953   363.0
    8+ hours  126.175   79.041   32.873   6.526   3.384   248.0
    Total     522.000  327.000  136.000  27.000  14.000  1026.0

- The following code calculates the chi-squared tets statistic using the two two-way tables above.
```python
#Chi Squared Test Statistic
#Note: Five out of the twenty (25%) expected counts are under 5. For general purposes, the chi quared test will be continued, but we should be cautious of the results.

row_num, col_num = two_way_tab2.shape

chi_sq_stat = 0
for a in range(row_num - 1):
  for b in range(col_num - 1):
    partial = ((two_way_tab[a, b] - two_way_tab2[a, b])**2)/two_way_tab2[a, b]
    chi_sq_stat += partial

print("The chi-squared statistic for this test is: " + str(chi_sq_stat))
```
    The chi-squared statistic for this test is: 14.687197372831275

- The test statistic of 14.687 with a df = 12 yields a p-value far above 0.05 so we fail to reject Ho. There is not convincing evidence at the 5% alpha level that there is an association between external light exposure (1-5) and sleep time (hrs) in individuals like those surveyed. Upon closer examination, somewhat noticeable changes only occur in the group that responded with a '5' to the external light exposure question.

## 4.3 Sleep Quality (1-5) vs. Light Exposure (1-5)
- To investigate the relationship between these two categorical variables, a segmented bar graph was constructed.
```python
#Relationship Between Light Exposure and Sleep Quality
mlig1 = []
mlig2 = []
mlig3 = []
mlig4 = []
mlig5 = []

for a in range(len(light_exp)):
  if light_exp[a] == 1:
    mlig1.append(sleep_quality[a])
  if light_exp[a] == 2:
    mlig2.append(sleep_quality[a])
  if light_exp[a] == 3:
    mlig3.append(sleep_quality[a])
  if light_exp[a] == 4:
    mlig4.append(sleep_quality[a])
  if light_exp[a] == 5:
    mlig5.append(sleep_quality[a])

mlig1_summary = []
mlig2_summary = []
mlig3_summary = []
mlig4_summary = []
mlig5_summary = []

ml1s1, ml2s1, ml3s1, ml4s1, ml5s1 = 0, 0, 0, 0, 0
ml1s2, ml2s2, ml3s2, ml4s2, ml5s2 = 0, 0, 0, 0, 0
ml1s3, ml2s3, ml3s3, ml4s3, ml5s3 = 0, 0, 0, 0, 0
ml1s4, ml2s4, ml3s4, ml4s4, ml5s4 = 0, 0, 0, 0, 0
ml1s5, ml2s5, ml3s5, ml4s5, ml5s5 = 0, 0, 0, 0, 0

for a in range(len(mlig1)):
  if mlig1[a] == 1:
    ml1s1 += 1/len(mlig1)
  elif mlig1[a] == 2:
    ml2s1 += 1/len(mlig1)
  elif mlig1[a] == 3:
    ml3s1 += 1/len(mlig1)
  elif mlig1[a] == 4:
    ml4s1 += 1/len(mlig1)
  elif mlig1[a] == 5:
    ml5s1 += 1/len(mlig1)

for a in range(len(mlig2)):
  if mlig2[a] == 1:
    ml1s2 += 1/len(mlig2)
  elif mlig2[a] == 2:
    ml2s2 += 1/len(mlig2)
  elif mlig2[a] == 3:
    ml3s2 += 1/len(mlig2)
  elif mlig2[a] == 4:
    ml4s2 += 1/len(mlig2)
  elif mlig2[a] == 5:
    ml5s2 += 1/len(mlig2)

for a in range(len(mlig3)):
  if mlig3[a] == 1:
    ml1s3 += 1/len(mlig3)
  elif mlig3[a] == 2:
    ml2s3 += 1/len(mlig3)
  elif mlig3[a] == 3:
    ml3s3 += 1/len(mlig3)
  elif mlig3[a] == 4:
    ml4s3 += 1/len(mlig3)
  elif mlig3[a] == 5:
    ml5s3 += 1/len(mlig3)

for a in range(len(mlig4)):
  if mlig4[a] == 1:
    ml1s4 += 1/len(mlig4)
  elif mlig4[a] == 2:
    ml2s4 += 1/len(mlig4)
  elif mlig4[a] == 3:
    ml3s4 += 1/len(mlig4)
  elif mlig4[a] == 4:
    ml4s4 += 1/len(mlig4)
  elif mlig4[a] == 5:
    ml5s4 += 1/len(mlig4)

for a in range(len(mlig5)):
  if mlig5[a] == 1:
    ml1s5 += 1/len(mlig5)
  elif mlig5[a] == 2:
    ml2s5 += 1/len(mlig5)
  elif mlig5[a] == 3:
    ml3s5 += 1/len(mlig5)
  elif mlig5[a] == 4:
    ml4s5 += 1/len(mlig5)
  elif mlig5[a] == 5:
    ml5s5 += 1/len(mlig5)

#Categorical v. Categorical
labels = ['1', '2', '3', '4', '5']

g1 = [ml1s1, ml1s2, ml1s3, ml1s4, ml1s5]
g2 = [ml2s1, ml2s2, ml2s3, ml2s4, ml2s5]
g3 = [ml3s1, ml3s2, ml3s3, ml3s4, ml3s5]
g4 = [ml4s1, ml4s2, ml4s3, ml4s4, ml4s5]
g5 = [ml5s1, ml5s2, ml5s3, ml5s4, ml5s5]

width = 0.35       # the width of the bars: can also be len(x) sequence

fig, ax = plt.subplots()

ax.bar(labels, g1, width)
ax.bar(labels, g2, width, bottom = g1)
ax.bar(labels, g3, width, bottom = list(np.array(g1)+np.array(g2)))
ax.bar(labels, g4, width, bottom = list(np.array(g1)+np.array(g2)+np.array(g3)))
ax.bar(labels, g5, width, bottom = list(np.array(g1)+np.array(g2)+np.array(g3)+np.array(g4)))

ax.set_ylabel('Distribution of Sleep Quality Responses (1-5)')
ax.set_xlabel('External Light Exposure (1-5)')
ax.set_title('Distributions of Sleep Quality Responses (1-5) for Varying Levels of External Light Exposure (1-5)')

print("Blue: Frequency of '1' responses") 
print("Orange: Frequency of '2' responses")
print("Green: Frequency of '3' responses") 
print("Red: Frequency of '4' responses")
print("Purple: Frequency of '5' responses") 
print('')
plt.show()
```
    Blue: Frequency of '1' responses
    Orange: Frequency of '2' responses
    Green: Frequency of '3' responses
    Red: Frequency of '4' responses
    Purple: Frequency of '5' responses

![img6](https://user-images.githubusercontent.com/97144011/168626964-c2b0dc2e-84eb-47e1-9f7d-70b3bc6b00bd.png)
- An alternative representation of the data is shown below.
```python
#Multiple Bar Charts Representation

N = 5
ind = np.arange(N) 
width = 0.15
  
bar1 = plt.bar(ind, g1, width)  
bar2 = plt.bar(ind+width, g2, width)
bar3 = plt.bar(ind+width*2, g3, width)
bar4 = plt.bar(ind+width*3, g4, width)
bar5 = plt.bar(ind+width*4, g5, width)
  
  
plt.xticks(ind+width,['1', '2', '3', '4', '5'])

plt.xlabel('Light Exposure (1-5)')
plt.ylabel('Frequency within Light Exposure Group')
plt.title('Distributions of Sleep Quality Responses (1-5) for Varying Levels of External Light Exposure (1-5)')

print("Blue: Frequency of '1' responses for sleep quality") 
print("Orange: Frequency of '2' responses for sleep quality")
print("Green: Frequency of '3' responses for sleep quality") 
print("Red: Frequency of '4' responses for sleep quality")
print("Purple: Frequency of '5' responses for sleep quality") 
print('')
plt.show()
```
    Blue: Frequency of '1' responses for sleep quality
    Orange: Frequency of '2' responses for sleep quality
    Green: Frequency of '3' responses for sleep quality
    Red: Frequency of '4' responses for sleep quality
    Purple: Frequency of '5' responses for sleep quality

![img7](https://user-images.githubusercontent.com/97144011/168627130-7ea22fe7-0c30-4750-b6d8-73c708fd6522.png)
- Notice that the proportion of '1' and '2' responses for sleep quality are much higher for those who responded with a '5' to the external light exposure question. We investigated this further by conducting a chi squared test for independence.
```python
#Chi Squared Test for Independence

r1c1 = []
r1c2 = []
r1c3 = []
r1c4 = []
r1c5 = []

r2c1 = []
r2c2 = []
r2c3 = []
r2c4 = []
r2c5 = []

r3c1 = []
r3c2 = []
r3c3 = []
r3c4 = []
r3c5 = []

r4c1 = []
r4c2 = []
r4c3 = []
r4c4 = []
r4c5 = []

r5c1 = []
r5c2 = []
r5c3 = []
r5c4 = []
r5c5 = []

row1 = [r1c1, r1c2, r1c3, r1c4, r1c5]
row2 = [r2c1, r2c2, r2c3, r2c4, r2c5]
row3 = [r3c1, r3c2, r3c3, r3c4, r3c5]
row4 = [r4c1, r4c2, r4c3, r4c4, r4c5]
row5 = [r5c1, r5c2, r5c3, r5c4, r5c5]

for a in range(len(sleep_quality)):
  for b in range(len(row1)):
    if sleep_quality[a] == 1 and light_exp[a] == b+1:
      row1[b].append(a)
    if sleep_quality[a] == 2 and light_exp[a] == b+1:
      row2[b].append(a)
    if sleep_quality[a] == 3 and light_exp[a] == b+1:
      row3[b].append(a)
    if sleep_quality[a] == 4 and light_exp[a] == b+1:
      row4[b].append(a)
    if sleep_quality[a] == 5 and light_exp[a] == b+1:
      row5[b].append(a)

for a in range(len(row1)):
  row1[a] = len(row1[a])
  row2[a] = len(row2[a])
  row3[a] = len(row3[a])
  row4[a] = len(row4[a])
  row5[a] = len(row5[a])

two_way_tab = np.array([row1, row2, row3, row4, row5])

row_sums = np.array([[sum(row1)], [sum(row2)], [sum(row3)], [sum(row4)], [sum(row5)], [1026]])

column_sums = np.array([np.add(np.add(np.add(np.add(np.array(row1), np.array(row2)), np.array(row3)), np.array(row4)), np.array(row5))])
two_way_tab = np.append(two_way_tab, column_sums, axis=0)
two_way_tab = np.append(two_way_tab, row_sums, axis=1)

two_way_df = pd.DataFrame(two_way_tab)
two_way_df.columns = ["L1", "L2", "L3", "L4", "L5", "Total"]
two_way_df.index = ["1", "2", "3", "4","5", "Total"]

print("Observed Counts for Relative Frequency of Sleep Quality (1-5) vs. External Light Exposure (1-5)")
print(two_way_df)

#Expected Counts
two_way_tab2 = np.empty((5, 5), float)

row_sums_list = [sum(row1), sum(row2), sum(row3), sum(row4), sum(row5)]
column_sums_list = list(two_way_tab[5, 0:5])

for a in range(len(row_sums_list)):
  for b in range(len(column_sums_list)):
    two_way_tab2[a, b] = float(two_way_tab2[a, b])
    two_way_tab2[a, b] = np.around(float(row_sums_list[a]*column_sums_list[b]/1026), 3)

two_way_tab2 = np.append(two_way_tab2, column_sums, axis=0)
two_way_tab2 = np.append(two_way_tab2, row_sums, axis=1)

two_way_dfE = pd.DataFrame(two_way_tab2)
two_way_dfE.columns = ["L1", "L2", "L3", "L4", "L5", "Total"]
two_way_dfE.index = ["1", "2", "3", "4","5", "Total"]

print("")
print("Expected Counts for Relative Frequency of Sleep Quality (1-5) vs. External Light Exposure (1-5)")
print(two_way_dfE)
```
    Observed Counts for Relative Frequency of Sleep Quality (1-5) vs. External Light Exposure (1-5)
            L1   L2   L3  L4  L5  Total
    1       15    7    2   2   3     29
    2       44   32   11   1   3     91
    3      155  112   75   9   5    356
    4      211  136   43  14   2    406
    5       97   40    5   1   1    144
    Total  522  327  136  27  14   1026

    Expected Counts for Relative Frequency of Sleep Quality (1-5) vs. External Light Exposure (1-5)
                L1       L2       L3      L4      L5   Total
    1       14.754    9.243    3.844   0.763   0.396    29.0
    2       46.298   29.003   12.062   2.395   1.242    91.0
    3      181.123  113.462   47.189   9.368   4.858   356.0
    4      206.561  129.398   53.817  10.684   5.540   406.0
    5       73.263   45.895   19.088   3.789   1.965   144.0
    Total  522.000  327.000  136.000  27.000  14.000  1026.0
- The following code calculates the chi squared tets statistic for this test:
```python
#Chi Squared Test Statistic
#Note: Eight out of the twenty (40%) expected counts are under 5. Be cautious of the following results.

row_num, col_num = two_way_tab2.shape

chi_sq_stat = 0
for a in range(row_num - 1):
  for b in range(col_num - 1):
    partial = ((two_way_tab[a, b] - two_way_tab2[a, b])**2)/two_way_tab2[a, b]
    chi_sq_stat += partial

print("The chi-squared statistic for this test is: " + str(chi_sq_stat))
```
    The chi-squared statistic for this test is: 71.8455958459828
- The test statistic of 71.846 with a df = 16 yields a p-value far above 0.05 so we reject Ho. There is convincing evidence at the 5% alpha level that there is an association between external light exposure (1-5) and sleep quality (1-5) in individuals like those surveyed.

## 4.4 Sleep Proportion
- We now define the variable sleep proportion, which is the ratio of the estimated actual sleep time to the total time in bed. Note that due to estimation, some proportions were over 1. These values were rounded down to 1 for the purpose of data use.

```python
#Ratio of Sleep Time to Time in Bed
bed_week = array_sleep[:, 25]
bed_weekend = array_sleep[:, 33]

for a in range(len(bed_week)):
  if isNaN(bed_week[a]) == True:
    bed_week[a] = 475.3
  if isNaN(bed_weekend[a]) == True:
    bed_weekend[a] = 524.25

avg_bed = list(np.add(bed_week*5/(60*7), bed_weekend*2/(60*7)))

#Make data
x = avg_bed

#Limits
xlow = min(avg_bed)
xhigh = max(avg_bed)

#Plot:
fig, ax = plt.subplots()

ax.hist(x, bins=32, linewidth=0.5, edgecolor="white")

ax.set(xlim=(0, xhigh))

plt.title("Distrbution of the Average Time in Bed (hrs) of 1029 Individuals (2015)")
plt.xlabel("Hours")
plt.ylabel("Frequency")

plt.show()
```
![img8](https://user-images.githubusercontent.com/97144011/168628600-4b1a649f-bdd2-4f6a-a1ce-0f866bccd15e.png)

## 4.5 Sleep Proportion vs. Sleep Quality (1-5)
- Afterward, we investigated the relationship between sleep quality scores and sleep proportion.
```python
#Ratio of Estimated Actual Sleep Time to Time in Bed
ratio_sb = np.divide(avg_total, avg_bed)
ratio_sb=list(ratio_sb)

#Account for Discrepancy from Estimation
for a in range(len(ratio_sb)):
  if ratio_sb[a] > 1:
    ratio_sb[a] = 1

avg1 = []
avg2 = []
avg3 = []
avg4 = []
avg5 = []

for a in range(len(sleep_quality)):
  if sleep_quality[a] == 1:
    avg1.append(ratio_sb[a])
  if sleep_quality[a] == 2:
    avg2.append(ratio_sb[a])
  if sleep_quality[a] == 3:
    avg3.append(ratio_sb[a])
  if sleep_quality[a] == 4:
    avg4.append(ratio_sb[a])
  if sleep_quality[a] == 5:
    avg5.append(ratio_sb[a])

#Quantitative v. Categorical
# make data:
D = np.array([avg1, avg2, avg3, avg4, avg5], dtype=object)
labels = ["1", "2", "3", "4", "5"]

# plot
fig, ax = plt.subplots()
VP = ax.boxplot(D, labels = labels, positions=[2, 4, 6, 8, 10], widths=1.5, patch_artist=True,
                showmeans=False, showfliers=False,
                medianprops={"color": "white", "linewidth": 0.5},
                boxprops={"facecolor": "C1", "edgecolor": "white",
                          "linewidth": 0.5},
                whiskerprops={"color": "C1", "linewidth": 1.5},
                capprops={"color": "C1", "linewidth": 1.5})
plt.xlabel("Sleep Quality Score (1-5)")
plt.ylabel("Sleep Proportion")
plt.title("Distribution of Sleep Proportion for Varying Sleep Quality Scores (1-5) of 1029 Individuals (2015)")

plt.show()
```
![img9](https://user-images.githubusercontent.com/97144011/168629396-cc3bebbc-eb80-468b-95c7-14f1d38791fb.png)

- To further investigate this relationship, we conducted a chi-squared test for independence to determine if there was convincing evidence at the 5% level for a difference in distributions of sleep times for varying sleep quality scores. The following code creates the observed and expected counts tables.
```python
#Chi Squared Test for Independence

#Observed Counts
r1c1 = []
r1c2 = []
r1c3 = []
r1c4 = []
r1c5 = []

r2c1 = []
r2c2 = []
r2c3 = []
r2c4 = []
r2c5 = []

r3c1 = []
r3c2 = []
r3c3 = []
r3c4 = []
r3c5 = []

r4c1 = []
r4c2 = []
r4c3 = []
r4c4 = []
r4c5 = []

row1 = [r1c1, r1c2, r1c3, r1c4, r1c5]
row2 = [r2c1, r2c2, r2c3, r2c4, r2c5]
row3 = [r3c1, r3c2, r3c3, r3c4, r3c5]
row4 = [r4c1, r4c2, r4c3, r4c4, r4c5]

for a in range(len(ratio_sb)):
  for b in range(len(row1)):
    if ratio_sb[a] < 0.6 and sleep_quality[a] == b+1:
      row1[b].append(a)
    if 0.6 <= ratio_sb[a] < 0.7 and sleep_quality[a] == b+1:
      row2[b].append(a)
    if 0.7 <= ratio_sb[a] < 0.8 and sleep_quality[a] == b+1:
      row3[b].append(a)
    if 0.8 <= ratio_sb[a] and sleep_quality[a] == b+1:
      row4[b].append(a)

for a in range(len(row1)):
  row1[a] = len(row1[a])
  row2[a] = len(row2[a])
  row3[a] = len(row3[a])
  row4[a] = len(row4[a])

two_way_tab = np.array([row1, row2, row3, row4])

row_sums = np.array([[sum(row1)], [sum(row2)], [sum(row3)], [sum(row4)], [1029]])

column_sums = np.array([np.add(np.add(np.add(np.array(row1), np.array(row2)), np.array(row3)), np.array(row4))])
two_way_tab = np.append(two_way_tab, column_sums, axis=0)
two_way_tab = np.append(two_way_tab, row_sums, axis=1)

two_way_df = pd.DataFrame(two_way_tab)
two_way_df.columns = ["1", "2", "3", "4", "5", "Total"]
two_way_df.index = ["<6 hrs", "6-7 hrs", "7-8 hrs", "8+ hours", "Total"]

print("Observed Counts for Relative Frequency of Sleep Quality (1-5) vs. Sleep Proportion")
print(two_way_df)

#Expected Counts
two_way_tab2 = np.empty((4, 5), float)

row_sums_list = [sum(row1), sum(row2), sum(row3), sum(row4)]
column_sums_list = list(two_way_tab[4, 0:5])

for a in range(len(row_sums_list)):
  for b in range(len(column_sums_list)):
    two_way_tab2[a, b] = float(two_way_tab2[a, b])
    two_way_tab2[a, b] = np.around(float(row_sums_list[a]*column_sums_list[b]/1029), 3)

two_way_tab2 = np.append(two_way_tab2, column_sums, axis=0)
two_way_tab2 = np.append(two_way_tab2, row_sums, axis=1)

two_way_dfE = pd.DataFrame(two_way_tab2)
two_way_dfE.columns = ["1", "2", "3", "4", "5", "Total"]
two_way_dfE.index = ["<0.6", "0.6-0.7", "0.7-0.8", "0.8+", "Total"]

print("")
print("Expected Counts for Relative Frequency of Sleep Quality (1-5) vs. Sleep Proportion")
print(two_way_dfE)
```
    Observed Counts for Relative Frequency of Sleep Quality (1-5) vs. Sleep Proportion
               1   2    3    4    5  Total
    <6 hrs     5  10   12   10    3     40
    6-7 hrs    4  10   28   12    2     56
    7-8 hrs    8  20   78   35    4    145
    8+ hours  12  51  240  349  136    788
    Total     29  91  358  406  145   1029

    Expected Counts for Relative Frequency of Sleep Quality (1-5) vs. Sleep Proportion
                  1       2        3        4        5   Total
    <0.6      1.127   3.537   13.916   15.782    5.637    40.0
    0.6-0.7   1.578   4.952   19.483   22.095    7.891    56.0
    0.7-0.8   4.086  12.823   50.447   57.211   20.432   145.0
    0.8+     22.208  69.687  274.154  310.912  111.040   788.0
    Total    29.000  91.000  358.000  406.000  145.000  1029.0
- The following code calculates the chi-squared tets statistic using the two two-way tables above.
```python
#Chi Squared Test Statistic
#Note: Four out of the twenty (20%) expected counts are under 5. For general purposes, the chi quared test will be continued, but we should be cautious of the results.

row_num, col_num = two_way_tab2.shape

chi_sq_stat = 0
for a in range(row_num - 1):
  for b in range(col_num - 1):
    partial = ((two_way_tab[a, b] - two_way_tab2[a, b])**2)/two_way_tab2[a, b]
    chi_sq_stat += partial

print("The chi-squared statistic for ths test is: " + str(chi_sq_stat))
```
    The chi-squared statistic for ths test is: 119.219445429627
- The test statistic of 119.219 with a df = 12 yields a p-value far below 0.05 so we reject Ho. There is convincing evidence at the 5% alpha level that there is an association between sleep quality (1-5) and sleep proportion in individuals like those surveyed.

## 4.6 Actual Sleep Times (hrs) vs. Sleep Proportion
- Finally, a regression model between actual sleep time s(hrs) and sleep proprtion was created to visualize the correlation between the two variables.
```python
#Relationship Between Actual Sleep Time (hrs) and Sleep Proportion
outpr = reg(ratio_sb, avg_total)
r, a, b = outpr[2], outpr[3], outpr[4]
x = np.linspace(0.1, 1, 100)
y = a + b*x
plt.scatter(ratio_sb, avg_total, s=1.5, alpha=1)
plt.plot(x, y, color = 'black')
plt.xlabel("Sleep Proportion")
plt.ylabel('Actual Sleep Time (hrs)')
plt.title('Relationship Between Actual Seep Time (hrs) and Sleep Proportion')

print("LSRL: y = " + str(a) + " + " + str(b) + "x")
print("r = " + str(r))
plt.show()
```
    LSRL: y = 2.477111181612429 + 5.262424688649582x
    r = 0.5288522318690922
![img10](https://user-images.githubusercontent.com/97144011/168631527-2499fae4-cfee-4f2a-a097-51cb7538d6d7.png)
- The regression model reveals that there is a moderately strong, positive, linear association between sleep proportion and sleep time (hrs). We confirmed this by constructing a residual plot.
```python
#Residual Plot
outpr = reg(ratio_sb, avg_total)
r, a, b = outpr[2], outpr[3], outpr[4]

resids = []
for l in range(len(ratio_sb)):
  calc = avg_total[l] - (a+b*ratio_sb[l])
  resids.append(calc)

x = np.linspace(0.1, 1, 100)
y = 0*x

plt.scatter(ratio_sb, resids, s=1.5, alpha=1)
plt.plot(x, y, color = 'black')
plt.xlabel("Sleep Proportion")
plt.ylabel('Residual')
plt.title('Residual Plot for LSRL Relating Actual Seep Time (hrs) to X = Sleep Proportion')
plt.show()
```
![img11](https://user-images.githubusercontent.com/97144011/168633775-a5b4d147-e187-4112-bca7-3b793c0ff665.png)
- Since there is no leftover pattern in the residual plot and the residuals are randomly scattered around the residual line, a linear model between actual sleep time and x = sleep proportion is appropriate.

# 5. Conclusion
- Overall, it is evident that sleep deprivation is a prevalent issue in the United States. With the data showing that lower sleep quality scores are associated with shorter sleep times, action must be taken to combat this issue. Fortunately, reducing exposure to external light can potential improve sleep quality, as the data analysis found an association between external light exposure (1-5) and sleep quality (1-5).

