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
```

Step 2: Performing Linear Regression
Next, we'll use R to perform linear regression and predict crime rates based on socioeconomic factors.

```r
# Perform linear regression
crime_lm <- lm(CrimeRate ~ PovertyLevel + EducationLevel + PopulationDensity, data = crime_data)

# Summarize the regression results
summary(crime_lm)
```

## Step 3: Interpreting the Results

The summary of the regression results provides insights into the relationship between crime rates and the predictor variables (poverty level, education level, and population density).

```r
# Extract coefficients
coefficients <- coef(crime_lm)
print(coefficients)
```

## Step 4: Making Predictions

Now that we have our regression model, we can make predictions for new data points using the `predict()` function.

```r
# Generate predictions for new data
new_data <- data.frame(PovertyLevel = c(15, 10, 8),
                       EducationLevel = c(12, 14, 16),
                       PopulationDensity = c(5000, 8000, 10000))

predicted_crime <- predict(crime_lm, newdata = new_data)
print(predicted_crime)
```


