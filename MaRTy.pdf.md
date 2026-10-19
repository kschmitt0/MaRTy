---
title: "Assignment 2: MaRTy and the Many Grades of Shade"
author: "Kyle Schmitt"
format: 
    pdf:
        mainfont: "Times New Roman"
        sansfont: "Times New Roman"
        documentclass: article
        classoption: 12pt
        include-in-header: header.tex
        keep-md: true
execute:
    echo: false
    warning: false
    message: false
---





# Introduction

MaRTy is a mobile device used to measure mean radiant temperature (MRT) across a variety of locations and times. Data from MaRTy can be used to better inform our understanding of how pedestrians experience heat in different environments, as MaRTy directly collects information such as air temperature, wind speed, and humidity, while location data can indirectly tell us about things like what type of shade (if any) exists at that site and what type of ground surface is surrounding the device. This paper will analyze a dataset from MaRTy to answer the question, "How much does shade reduce pedestrian heat exposure, and are all types of shade equally effective?". The analysis was conducted in R without the use of AI.

# Air Temperature vs. MRT




::: {.cell}
::: {.cell-output-display}
![Differences between Air Temperature and MRT](MaRTy_files/figure-pdf/Create histogram-1.pdf)
:::
:::


The histogram shows us that broadly, air temperature and MRT are fairly similar. The largest columns are all clustered around 0, meaning there are a lot of results with only a small difference between the two variables. However, there is also a small but noticeable cluster of results with a larger difference (around 20-40), showing that there are plenty of exceptions where the two values diverge.

## Linear Regression

To answer the question of why two locations with similar air temperatures can have different MRT, a multiple linear regression model was created to measure the influence of different variables on the difference between air temperature and MRT. The independent variables selected for this model were Time_of_Day, Surface_Type, Shade_Category2, RH, and WindSpeed_ms because they are each representative of a unique measurement, avoiding the problem of multicollinearity.


::: {.cell}
::: {.cell-output .cell-output-stdout}

```

Call:
lm(formula = diff ~ Time_of_Day + Surface_Type + Shade_Category2 + 
    RH + WindSpeed_ms, data = marty)

Residuals:
    Min      1Q  Median      3Q     Max 
-15.492  -3.955   1.092   4.894  13.915 

Coefficients:
                                 Estimate Std. Error t value Pr(>|t|)    
(Intercept)                       1.70161    2.76766   0.615   0.5398    
Time_of_Day3:30 PM                0.07057    1.55420   0.045   0.9639    
Time_of_Day8:30 PM              -15.06639    1.52085  -9.907  < 2e-16 ***
Surface_Typegravel                2.75927    2.23682   1.234   0.2198    
Surface_Typeimpervious            2.88574    1.80529   1.598   0.1126    
Shade_Category2Engineered shade   4.88870    1.95633   2.499   0.0138 *  
Shade_Category2Sun-exposed       15.38850    1.76921   8.698 2.22e-14 ***
Shade_Category2Tree shade         2.05451    1.91307   1.074   0.2850    
RH                                0.05832    0.08882   0.657   0.5127    
WindSpeed_ms                      1.07224    0.87521   1.225   0.2229    
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 6.531 on 119 degrees of freedom
Multiple R-squared:  0.7022,	Adjusted R-squared:  0.6797 
F-statistic: 31.18 on 9 and 119 DF,  p-value: < 2.2e-16
```


:::
:::

\bigskip
The two most influential variables were 'Time_of_Day8:30 PM' and 'Shade_Category2Sun-exposed'. They both have p-values of the highest significance rating (confidence interval of 100%) and the highest coefficients in the model. 'Time_of_Day8:30 PM' has a coefficient of -15.46, meaning it causes the difference between air temperature and MRT to go in a negative direction, while 'Shade_Category2Sun-exposed' has a coefficient of 15.46, causing the difference to increase. The variable 'Shade_Category2Engineered shade' also had a statistically significant influence, with a coefficient of 4.99 and a confidence interval of 99%, meaning it has a moderate effect of increasing the difference between air temperature and MRT. Overall, this model has an adjusted R-squared of 0.68, meaning it explains about 68% of variation in the difference between air temperature and MRT.


::: {.cell tbl-cap='VIF Analysis'}
::: {.cell-output-display}


|                |     GVIF| Df| GVIF^(1/(2*Df))|
|:---------------|--------:|--:|---------------:|
|Time_of_Day     | 1.682624|  2|        1.138929|
|Surface_Type    | 1.527595|  2|        1.111737|
|Shade_Category2 | 1.705605|  3|        1.093066|
|RH              | 1.620202|  1|        1.272872|
|WindSpeed_ms    | 1.194580|  1|        1.092968|


:::
:::


The VIF analysis shows that none of the variables have a VIF of greater than 2.0, much lower than the typical threshold of 5.0, confirming that multicollinearity is not an issue with this model.


::: {.cell tbl-cap='ANOVA Analysis'}
::: {.cell-output-display}


|                |  Df|     Sum Sq|    Mean Sq|    F value|    Pr(>F)|
|:---------------|---:|----------:|----------:|----------:|---------:|
|Time_of_Day     |   2| 6687.40942| 3343.70471| 78.4013320| 0.0000000|
|Surface_Type    |   2|  254.17746|  127.08873|  2.9799061| 0.0546019|
|Shade_Category2 |   3| 4941.17763| 1647.05921| 38.6193301| 0.0000000|
|RH              |   1|   21.54406|   21.54406|  0.5051531| 0.4786353|
|WindSpeed_ms    |   1|   64.01199|   64.01199|  1.5009177| 0.2229496|
|Residuals       | 119| 5075.17985|   42.64857|         NA|        NA|


:::
:::


This ANOVA analysis confirms that there is a large variance in the Time_of_Day and Shade_Category2 categorical variables, as shown by their high F values and low p-values. Surface_Type also shows up as the next most impactful category, but with a much smaller F value and a confidence interval of around 94.5%, just under the usual threshold for statistical significance.

# How Much Does Shade Matter?


::: {.cell}
::: {.cell-output-display}
![Mean MRT by Shade Category](MaRTy_files/figure-pdf/bar chart-1.pdf)
:::
:::


This bar chart shows how average MRT compares across each shade category. From this, we can see that built shade has the lowest average MRT, followed closely by tree shade, with engineered shade having the highest average MRT among types of shade, but still much lower than the average MRT in sun-exposed areas.

# Does Time of Day Matter?




::: {.cell}
::: {.cell-output-display}
![Average MRT over Time](MaRTy_files/figure-pdf/time of day line graph-1.pdf)
:::
:::


This line chart shows the difference in the average MRT of shaded versus exposed areas over time - measurements were taken at 12:30 PM (1230), 3:30 PM (1530), and 8:30 PM (2030). From this graphic, we can see that between 12:30 and 3:30, the MRT of sun-exposed areas sharply rises while in shaded areas it stays flat. However, by 8:30, we see that the average temperature of sun-exposed areas actually falls below that of shaded areas. Naturally, shade has a much greater impact on MRT during the day when it can block the sun's radiation than at night when all areas are 'shaded'. 

# Site Comparison


::: {.cell tbl-cap='Site Comparison'}
::: {.cell-output-display}


|\makecell{Shade\\Category} |\makecell{Surface\\Cover} |\makecell{Time of\\Day} |    RH| \makecell{Wind\\Speed}| \makecell{Air\\Temp (C)}| \makecell{MRT\\(C)}|
|:--------------------------|:-------------------------|:-----------------------|-----:|----------------------:|------------------------:|-------------------:|
|Tree shade                 |gravel                    |8:30 PM                 |  7.93|                   0.40|                    37.61|               35.08|
|Sun-exposed                |concrete rough            |12:30 PM                | 10.79|                   0.45|                    35.30|               59.94|


:::
:::


These two sites have very similar air temperatures (37.61C and 35.30C) and very different MRTs (35.08C and 59.94C). One is a tree-shaded gravel site from 8:30PM, and the other is a sun-exposed concrete site at 12:30PM. They both have fairly low relative humidity (7.93 and 10.79) and similar wind speeds (0.40 and 0.45). Gravel and concrete are both impervious surfaces, so the biggest differences between the sites are shade type and time of day. This is consistent with the regression model built earlier which found 'Time_of_Day8:30 PM' and 'Shade_Category2Sun-exposed' to be the most influential variables in determining the difference between air temperature and MRT.

# Implications for Urban Design

Based on this data from MaRTy, it is evident that the biggest factor planners and designers have control over in improving thermal comfort for pedestrians is simply the presence of shade. This is demonstrated by the significance of sun exposure in the regression analysis as well as the site comparison which exemplified how the presence of shade can make a big difference regardless of surrounding environmental factors such as surface type, humidity, or wind speed. 

The best strategy for increasing shade will be different for different cities. For example, a desert city like Phoenix may have relatively high barriers to implementing a tree canopy-based shade plan due to water scarcity and extreme heat damaging growing trees, while a rural town in Vermont may find planting trees to be a more cost-efficient strategy than building engineered shade structures. 

While city-specific factors like this will play a dominant role in guiding urban design choices for increasing shade, the MaRTy data does tell us that shade from built structures has the lowest MRT of all shade types. This is great news for cities because built structures deliver numerous other benefits besides shade - they are housing, businesses, transportation infrastructure - all things that are desirable to have. 

However, buildings are not typically designed in a way that deliberately maximizes shade for pedestrians. This is where more intentional work by planners and designers is required. Planners should update zoning codes with features like increasing densities in residential areas and decreasing minimum setback requirements so that buildings become taller and closer to the sidewalk, producing more shade for pedestrians. These changes would also work to solve other problems such as improving housing affordability and decreasing environmentally harmful sprawling development patterns. Designers should take into account more nuanced details when designing spaces for pedestrians such as decreasing impervious surfaces which retain and reflect more heat than natural surfaces, or ensuring that shade-providing structures do not block the cooling effects of wind. Even if these variables have a smaller impact statistically, all together they can add up to make a meaningful difference.