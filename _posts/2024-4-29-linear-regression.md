---
layout: post
title: Linear regression
date: April 29, 2024
---

Linear regression might sound like a daunting term, but its application is straightforward and powerful, especially when it comes to understanding and predicting real-world phenomena like crime rates.

## Step 1: Simulating Example Data

First, we'll generate synthetic data to represent crime rates and socioeconomic factors. In our example, we'll simulate data for poverty level, education level, population density, and crime rate.

```r
# Set seed for reproducibility
set.seed(123)

# Generate example data
n <- 100  # Number of observations
PovertyLevel <- rnorm(n, mean = 10, sd = 2)  # Simulate poverty level data
EducationLevel <- rnorm(n, mean = 12, sd = 3)  # Simulate education level data
PopulationDensity <- rnorm(n, mean = 5000, sd = 1000)  # Simulate population density data

# Generate crime rate data based on simulated predictors
CrimeRate <- 10 + 0.5*PovertyLevel + 0.2*EducationLevel + 0.1*PopulationDensity + rnorm(n, mean = 0, sd = 5)

# Create a data frame
crime_data <- data.frame(CrimeRate, PovertyLevel, EducationLevel, PopulationDensity)

head(crime_data)
```

```
  CrimeRate PovertyLevel EducationLevel PopulationDensity
1  732.7181     8.879049       9.868780          7198.810
2  644.8018     9.539645      12.770651          6312.413
3  487.6035    13.117417      11.259924          4734.855
4  566.3188    10.141017      10.957372          5543.194
5  473.3385    10.258575       9.145144          4585.660
6  473.1193    13.430130      11.864917          4523.753
...
```

Step 2: Performing Linear Regression
Next, we'll use R to perform linear regression and predict crime rates based on socioeconomic factors.

```r
# Perform linear regression
crime_lm <- lm(CrimeRate ~ PovertyLevel + EducationLevel + PopulationDensity, data = crime_data)

# Summarize the regression results
summary(crime_lm)
```

```
Call:
lm(formula = CrimeRate ~ PovertyLevel + EducationLevel + PopulationDensity, 
    data = crime_data)

Residuals:
     Min       1Q   Median       3Q      Max 
-12.4569  -3.2696   0.2832   3.3516  12.6605 

Coefficients:
                   Estimate Std. Error t value Pr(>|t|)    
(Intercept)       1.180e+01  4.939e+00   2.389   0.0188 *  
PovertyLevel      3.614e-01  2.922e-01   1.237   0.2192    
EducationLevel    2.770e-01  1.824e-01   1.519   0.1322    
PopulationDensity 9.971e-02  5.612e-04 177.693   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 5.258 on 96 degrees of freedom
Multiple R-squared:  0.997,	Adjusted R-squared:  0.9969 
F-statistic: 1.07e+04 on 3 and 96 DF,  p-value: < 2.2e-16
```

The summary of the regression results provides insights into the relationship between crime rates and the predictor variables (poverty level, education level, and population density).

