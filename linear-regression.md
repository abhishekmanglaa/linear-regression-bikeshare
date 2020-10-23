Linear Regression Bikeshare
================

The goal of this notebookis to review the fundamental concepts of
regression modelling. We will also begin to practice using the
statistical programming language R.

\-\> There are two goals. The first is to predict the variable COUNT as
a function of the other variables. The second is to determine the effect
of bad weather on the number of bikes rented.

## Part 1: Wrangling and Exploratory Data Analysis

Adding a new variable BADWEATHER, which is “YES” if there is light or
heavy rain or snow (if WEATHERSIT is 3 or 4), and “NO” otherwise (if
WEATHERSIT is 1 or 2).

``` r
bikeShare$WEATHERSIT <- as.numeric(bikeShare$WEATHERSIT)
bikeShare$BADWEATHER <- ifelse(bikeShare$WEATHERSIT > 2 , "YES", "NO")

#ScatterPlot for COUNT
ggplot(bikeShare, aes(ATEMP,COUNT, color = BADWEATHER)) + geom_point()
```

![](linear-regression_files/figure-gfm/Scatter%20Plots-1.png)<!-- -->

``` r
#Scotter Plot for CASUAL users
ggplot(bikeShare, aes(ATEMP,CASUAL, color = BADWEATHER)) + geom_point()
```

![](linear-regression_files/figure-gfm/Scatter%20Plots-2.png)<!-- -->

``` r
#Scatter Plot for RESGISTERED users
ggplot(bikeShare, aes(ATEMP,REGISTERED, color = BADWEATHER)) + geom_point()
```

![](linear-regression_files/figure-gfm/Scatter%20Plots-3.png)<!-- -->

As we observe that the count of bike rides forms a curve. The count of
rides is maximum when ATEMP is ~ 0.6 which approximates to 24 degree
Celsius, which is pleasant weather.

In case of registered users, we see the trend similar to the count of
total rides. But for casual rides the curve is different. Also, we can
see that registered users are more active even on bad weather days.

## Part 2: Basic Regression

Running a multivariate regression for COUNT, using the factors listed as
well as TEMP, ATEMP, HUMIDITY, and WINDSPEED.

``` r
bikeShare$BADWEATHER <- as.factor(bikeShare$BADWEATHER)
Regression1 <- lm(COUNT~MONTH + WEEKDAY + HOLIDAY + TEMP + ATEMP + 
                    HUMIDITY + WINDSPEED, 
                    data = bikeShare)
summary(Regression1)
```

    ## 
    ## Call:
    ## lm(formula = COUNT ~ MONTH + WEEKDAY + HOLIDAY + TEMP + ATEMP + 
    ##     HUMIDITY + WINDSPEED, data = bikeShare)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -5400.6 -1001.0  -183.8  1096.2  3457.9 
    ## 
    ## Coefficients:
    ##                Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)     4568.38     455.96  10.019  < 2e-16 ***
    ## MONTHAUGUST     -633.37     298.39  -2.123  0.03413 *  
    ## MONTHDECEMBER     42.59     264.00   0.161  0.87188    
    ## MONTHFEBRUARY   -752.73     270.23  -2.786  0.00549 ** 
    ## MONTHJANUARY    -722.00     287.42  -2.512  0.01223 *  
    ## MONTHJULY      -1267.77     313.63  -4.042 5.87e-05 ***
    ## MONTHJUNE       -563.97     287.41  -1.962  0.05012 .  
    ## MONTHMARCH      -286.83     245.56  -1.168  0.24317    
    ## MONTHMAY         144.33     256.82   0.562  0.57430    
    ## MONTHNOVEMBER    437.12     253.84   1.722  0.08550 .  
    ## MONTHOCTOBER     771.43     244.51   3.155  0.00167 ** 
    ## MONTHSEPTEMBER   444.71     267.86   1.660  0.09731 .  
    ## WEEKDAYYES        71.13     109.18   0.651  0.51494    
    ## HOLIDAYYES      -745.85     297.06  -2.511  0.01227 *  
    ## TEMP            5779.00    2377.96   2.430  0.01533 *  
    ## ATEMP           1688.42    2491.75   0.678  0.49824    
    ## HUMIDITY       -4244.54     378.61 -11.211  < 2e-16 ***
    ## WINDSPEED      -4692.14     689.88  -6.801 2.19e-11 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1321 on 713 degrees of freedom
    ## Multiple R-squared:  0.546,  Adjusted R-squared:  0.5352 
    ## F-statistic: 50.44 on 17 and 713 DF,  p-value: < 2.2e-16

``` r
#Predict the COUNT
sum(Regression1$coefficients*c(1,0,0,0,1,0,0,0,0,0,0,0,1,0,0.35,0.30,0.21,0.30,1))
```

    ## Warning in Regression1$coefficients * c(1, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, :
    ## longer object length is not a multiple of shorter object length

    ## [1] 8716.079

The p-value of BADWEATHERYES is very low, hence it has a significant
effect on the count of bikeshare requests. The coefficient if 1701,
means on average if the weather is not bad, the count is 1701 more than
if it was bad.

## Part 3: More Regression Modelling

1)  Running a simple regression to determine the effect of bad weather
    on COUNT when other variables are not included in the model.

<!-- end list -->

``` r
Regression2 <- lm(COUNT~BADWEATHER, data = bikeShare)
summary(Regression2)
```

    ## 
    ## Call:
    ## lm(formula = COUNT ~ BADWEATHER, data = bikeShare)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -4153.2 -1257.7     1.8  1404.8  4129.8 
    ## 
    ## Coefficients:
    ##               Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)    4584.24      70.63  64.908  < 2e-16 ***
    ## BADWEATHERYES -2780.95     416.69  -6.674 4.93e-11 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1882 on 729 degrees of freedom
    ## Multiple R-squared:  0.05758,    Adjusted R-squared:  0.05629 
    ## F-statistic: 44.54 on 1 and 729 DF,  p-value: 4.934e-11

The difference in the coefficients is due to the inclusion of other
variables. In the previous regression we had all the variables hence the
effect gets reduced.

2)  Regression with interaction term BADWEATHER\*WEEKDAY

<!-- end list -->

``` r
#Regression with interaction term BADWEATHER*WEEKDAY
Regression3 <- lm(COUNT~ BADWEATHER + WEEKDAY + BADWEATHER*WEEKDAY, data = bikeShare)
summary(Regression3)
```

    ## 
    ## Call:
    ## lm(formula = COUNT ~ BADWEATHER + WEEKDAY + BADWEATHER * WEEKDAY, 
    ##     data = bikeShare)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -4206.7 -1262.1    -3.7  1405.3  4261.5 
    ## 
    ## Coefficients:
    ##                          Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)                4452.5      131.5  33.861  < 2e-16 ***
    ## BADWEATHERYES             -2637.1      852.2  -3.095  0.00205 ** 
    ## WEEKDAYYES                  185.3      155.9   1.188  0.23514    
    ## BADWEATHERYES:WEEKDAYYES   -201.2      977.1  -0.206  0.83695    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1883 on 727 degrees of freedom
    ## Multiple R-squared:  0.05941,    Adjusted R-squared:  0.05553 
    ## F-statistic: 15.31 on 3 and 727 DF,  p-value: 1.15e-09

We start by adding an interaction term BADWEATHER*WEEKDAY. Since the
coefficient of the interaction term BADWEATHER*WEEKDAY is negative, bike
share use is more negatively affected if it’s a weekday. But since the
p-value of the interaction term is 0.83, the significance is very low.

## Part 4: Even more Regression

1)  Running a regression with TEMP &
ATEMP

<!-- end list -->

``` r
Regression4 <- lm(COUNT ~ MONTH + HOLIDAY + WEEKDAY + BADWEATHER + TEMP + ATEMP
                  ,data = bikeShare)
summary(Regression4)
```

    ## 
    ## Call:
    ## lm(formula = COUNT ~ MONTH + HOLIDAY + WEEKDAY + BADWEATHER + 
    ##     TEMP + ATEMP, data = bikeShare)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -4320.9 -1086.0  -233.3  1193.3  3357.5 
    ## 
    ## Coefficients:
    ##                Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)     1619.86     380.94   4.252  2.4e-05 ***
    ## MONTHAUGUST     -348.14     306.96  -1.134  0.25711    
    ## MONTHDECEMBER   -156.94     269.77  -0.582  0.56092    
    ## MONTHFEBRUARY   -831.14     281.71  -2.950  0.00328 ** 
    ## MONTHJANUARY    -914.49     298.45  -3.064  0.00227 ** 
    ## MONTHJULY       -679.49     321.59  -2.113  0.03496 *  
    ## MONTHJUNE       -105.55     295.80  -0.357  0.72132    
    ## MONTHMARCH      -335.19     256.12  -1.309  0.19105    
    ## MONTHMAY          22.22     265.00   0.084  0.93319    
    ## MONTHNOVEMBER    438.14     261.99   1.672  0.09490 .  
    ## MONTHOCTOBER     746.61     250.48   2.981  0.00297 ** 
    ## MONTHSEPTEMBER   456.58     273.54   1.669  0.09552 .  
    ## HOLIDAYYES      -758.59     310.36  -2.444  0.01476 *  
    ## WEEKDAYYES        98.85     114.03   0.867  0.38630    
    ## BADWEATHERYES  -2696.63     310.01  -8.699  < 2e-16 ***
    ## TEMP            4401.10    2430.46   1.811  0.07059 .  
    ## ATEMP           1843.24    2537.36   0.726  0.46781    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1379 on 714 degrees of freedom
    ## Multiple R-squared:  0.5042, Adjusted R-squared:  0.4931 
    ## F-statistic: 45.38 on 16 and 714 DF,  p-value: < 2.2e-16

2)  Running a regression without TEMP & ATEMP

<!-- end list -->

``` r
Regression5 <- lm(COUNT ~ MONTH + HOLIDAY + WEEKDAY + BADWEATHER, 
                  data = bikeShare)
summary(Regression5)  
```

    ## 
    ## Call:
    ## lm(formula = COUNT ~ MONTH + HOLIDAY + WEEKDAY + BADWEATHER, 
    ##     data = bikeShare)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -4410.3 -1114.1  -275.5  1271.0  4528.2 
    ## 
    ## Coefficients:
    ##                Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)      4468.6      205.0  21.803  < 2e-16 ***
    ## MONTHAUGUST      1056.7      263.1   4.017 6.53e-05 ***
    ## MONTHDECEMBER   -1038.5      262.6  -3.955 8.42e-05 ***
    ## MONTHFEBRUARY   -1876.2      268.2  -6.995 6.08e-12 ***
    ## MONTHJANUARY    -2345.6      262.6  -8.931  < 2e-16 ***
    ## MONTHJULY        1031.2      262.6   3.927 9.44e-05 ***
    ## MONTHJUNE        1169.4      265.2   4.410 1.19e-05 ***
    ## MONTHMARCH       -822.3      262.8  -3.129  0.00183 ** 
    ## MONTHMAY          766.2      262.8   2.916  0.00366 ** 
    ## MONTHNOVEMBER    -175.6      265.0  -0.663  0.50771    
    ## MONTHOCTOBER      844.1      263.0   3.209  0.00139 ** 
    ## MONTHSEPTEMBER   1328.2      264.7   5.017 6.63e-07 ***
    ## HOLIDAYYES       -653.0      325.4  -2.007  0.04516 *  
    ## WEEKDAYYES        187.5      119.4   1.571  0.11672    
    ## BADWEATHERYES   -2795.2      324.7  -8.608  < 2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1450 on 716 degrees of freedom
    ## Multiple R-squared:  0.4507, Adjusted R-squared:   0.44 
    ## F-statistic: 41.96 on 14 and 716 DF,  p-value: < 2.2e-16

When we are trying to predict the value of COUNT, the model with higher
adjusted R-Squared is better (which has both ATEMP and TEMP), But if our
use case is just to make an inference TEMP and ATEMP do not have any
significance in determining the COUNT.
