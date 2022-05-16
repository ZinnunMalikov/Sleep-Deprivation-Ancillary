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
1. `Q10_a`: sleep quality, 5-point Likert Scale (1: very poor, 2: poor, 3: fair, 4: good, 5: very good)
2. `DOV_WEEKTIME`: average minutes spent in bed per day on weekdays
3. `DOV_WEEKEND`: average minutes spent in bed per day on weekends
4. `DOV_TOTALWEEK`: estimate of actual sleep obtained per day on weekdays (in minutes)
5. `DOV_TOTALWEEK`: estimate of actual sleep obtained per day on weekendss (in minutes)
6. `Q2_ampmA`: whether time went to bed on weekdays is a.m. or p.m.
7. `Q3_ampmA`: whether time went to bed on weekends is a.m. or p.m.
8. `Q14a`: how often do health problems make it difficult to get a good night’s sleep, 5-point Likert Scale (1: never, 2: rarely, 3: sometimes, 4: often, 5: always)
9. `Q15_a`: how often does outside noise (ex: street noise, sirens) make it more difficult to get a good night's sleep, 5-point Likert Scale (1: never, 2: rarely, 3: sometimes, 4: often, 5: always)
10. `Q15_b`: how often does inside noise (ex: television, other people, snoring) make it more difficult to get a good night's sleep, 5-point Likert Scale (1: never, 2: rarely, 3: sometimes, 4: often, 5: always)
11. `Q15_c`: how often does light (from either inside or outside) make it more difficult to get a good night's sleep, 5-point Likert Scale (1: never, 2: rarely, 3: sometimes, 4: often, 5: always)
12. `Q15_d`: how often does temperature (too hot or too cold) make it more difficult to get a good night's sleep, 5-point Likert Scale (1: never, 2: rarely, 3: sometimes, 4: often, 5: always)
13. `Q15_e`: how often does temperature (uncomfortable mattress) make it more difficult to get a good night's sleep, 5-point Likert Scale (1: never, 2: rarely, 3: sometimes, 4: often, 5: always)
14. `Q16`: how motivated to make sure have enough time to sleep, 5-point Likert Scale (1: extremely motivated, 2: very motivated, 3: somewhat motivated, 4: not that motivated, 5: not motivated at all)
15. `Q17`: how important a part of routine is going to bed at a suitable time, 5-point Likert Scale (1: extremely , 2: very important, 3: somewhat important, 4: not that important, 5: not important at all)
16. `Q22`: whether have been diagnosed sleep disorder (Yes/No)
17. `Q23`: answered only if answer for Q22 is 'Yes', what type of sleep disorder (3 choices: sleep apnea, insomnia, other sleep disorder)


```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/ZinnunMalikov/Sleep-Deprivation-Ancillary/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
