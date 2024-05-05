---
layout: post
title: detecting anomalies in race reporting during traffic stops
date: May 04, 2024
---

In a recent story by the San Francisco Chronicle, Paul Henderson, the Executive Director of the Department of Police Accountability, highlighted a troubling issue within the SFPD: the misreporting of race data during traffic and pedestrian stops. Such discrepancies not only challenge the integrity of vital databases intended to track racial disparities but also undermine trust between the community and law enforcement. As Henderson noted, the mislabeling of races or entering multiple racial categories to obscure actual demographic data are just some of the ways this misreporting manifests.

This news struck a chord with me, prompting a deeper reflection on the technological and methodological tools available to potentially uncover such discrepancies systematically. Could we enhance the oversight of police conduct by leveraging data science to detect when officers misreport race during traffic stops? In this post, I'll explore three innovative methods, applied on data I simulated, that could be employed to analyze traffic stop data, aiming to identify anomalies in how race is reported. I'll use three methods: chi2 analysis, cluster anlaysis, and logistic regression. Each offers a distinct lens to scrutinize the data for inconsistencies that might indicate misreporting.

These findings are crucial for departments seeking to ensure accurate and unbiased reporting. By identifying outliers through statistical methods, departments can target specific areas or officers for further training, review, and possibly even reevaluation of their data entry practices, ensuring that policy decisions are informed by reliable and fair data collection.

## 1. Statistical Analysis: Chi-Square Test
This method tests if the distribution of reported races by each officer is statistically different from the expected distribution based on the overall data. We can use a chi2 test to detect whether the frequency of each race reported by an officer is unusually high or low compared to what would be expected if their reports were distributed similarly to the overall population.

| ![](/images/2024-05-04-traffic-stops-anomoly-detection/chi2.png) | 
|:--:| 
| *This plot displays the results of a Chi-square test applied to the race reporting data of police officers, aimed at identifying those whose reporting patterns significantly differ from expected norms. Each point on the graph represents an officer, plotted against their badge ID on the x-axis and the corresponding p-value from the Chi-square test on the y-axis. The color coding is particularly telling: blue points indicate officers for whom the test did not find statistically significant anomalies in race reporting (p-value >= 0.05), suggesting their data aligns well with the overall distribution. Conversely, red points highlight officers (Badge IDs 1022 and 1041 in this case) whose reporting was found to be significantly different (p-value < 0.05), hinting at potential discrepancies in how they report the race of individuals during traffic stops.* |

## 2. Cluster Analysis: K-Means Clustering
K-means clustering groups data into clusters based on feature similarities. In our case, we can cluster officers based on the proportions of races they report. Outliers in this clustering might suggest anomalous behavior, such as consistently different reporting patterns from their peers.

## 3. Machine Learning: Logistic Regression Residuals
This method involves training a logistic regression model to predict the race of a subject based on non-racial features (e.g., time of day, age, gender). By examining the residuals (the difference between the predicted probability and the actual reported value), we can identify officers whose reports consistently differ from what the model predicts, suggesting possible misreporting.

```{r}
# load necessary libraries
library(dplyr)
library(ggplot2)
library(caret)
library(cluster)
library(tidyr)
library(reshape2)
library(ggrepel)
library(ggforce)

set.seed(123)  # set seed for reproducibility

# generate main data
n <- 10000  # Number of traffic stops
traffic_stops <- data.frame(
  traffic_stop_id = 1:n,
  time_of_day = sample(c("Morning", "Afternoon", "Evening", "Night"), n, replace = TRUE, prob = c(0.25, 0.30, 0.20, 0.25)),
  subject_age = sample(18:70, n, replace = TRUE),
  subject_gender = sample(c("Male", "Female"), n, replace = TRUE, prob = c(0.6, 0.4)),
  subject_race = sample(c("White", "Black", "Latino", "Asian"), n, replace = TRUE, prob = c(0.50, 0.20, 0.20, 0.10)),
  badge_id = sample(1001:1050, n, replace = TRUE),
  division = sample(c("North", "South", "East", "West"), n, replace = TRUE)
)

# introduce anomalies: certain officers misreport race
anomalous_officers <- sample(1001:1050, 5)  # 5 officers out of 50 are anomalous
anomaly_entries <- sample(1:n, 500)  # 500 entries out of 10,000 will be anomalies

# function to misreport race
misreport_race <- function(race) {
  race_sample <- c("White", "Black", "Latino", "Asian")
  return(sample(race_sample[race_sample != race], 1))
}

# apply anomalies to dataset
traffic_stops$subject_race[traffic_stops$badge_id %in% anomalous_officers & 1:n %in% anomaly_entries] <- 
  sapply(traffic_stops$subject_race[traffic_stops$badge_id %in% anomalous_officers & 1:n %in% anomaly_entries], misreport_race)

# 1. statistical analysis: chi2 test
# calculate observed counts of race reports per officer
officer_race_counts <- traffic_stops %>%
  group_by(badge_id, subject_race) %>%
  summarise(count = n(), .groups = 'drop')

# convert to wide format to facilitate calculations
wide_race_counts <- pivot_wider(officer_race_counts, names_from = subject_race, values_from = count, values_fill = list(count = 0))

# calculate total stops per officer to determine expected counts
wide_race_counts$total_stops <- rowSums(wide_race_counts[, -1])

# using overall race probabilities to calculate expected counts
overall_race_probs <- prop.table(table(traffic_stops$subject_race))
race_names <- names(overall_race_probs)

# expected counts for each race per officer based on their total stops
for(race in race_names) {
  wide_race_counts[[paste("expected", race, sep = "_")]] <- wide_race_counts$total_stops * overall_race_probs[race]
}

# calculate observed and expected counts for chi2 test
observed_and_expected <- wide_race_counts %>%
  mutate_at(vars(matches("^White$|^Black$|^Latino$|^Asian$")), list(observed = ~ .)) %>%
  mutate(across(starts_with("expected"), ~ as.numeric(.), .names = "{.col}_num")) %>%
  rowwise() %>%
  mutate(
    chi_square_result = list(chisq.test(
      x = c(White_observed, Black_observed, Latino_observed, Asian_observed),
      p = c(expected_White_num, expected_Black_num, expected_Latino_num, expected_Asian_num),
      rescale.p = TRUE
    ))
  ) %>%
  ungroup()

# extract p-values from the chi2 test results
observed_and_expected$chi_square_p_value <- sapply(observed_and_expected$chi_square_result, function(x) x$p.value)

# visualize the chi2 test results
ggplot(observed_and_expected, aes(x = badge_id, y = chi_square_p_value)) +
  geom_point(aes(color = chi_square_p_value < 0.05), alpha = 0.6) +  # Plot points with color based on significance
  geom_text_repel(
    aes(label = ifelse(chi_square_p_value < 0.05, as.character(badge_id), "")),
    box.padding = 0.35,  # Adjust padding within the text label background box
    point.padding = 0.5, # Distance from point to text
    segment.color = 'grey50', # Color of the line connecting text and point
    size = 3.5,  # Text size, can adjust to fit your specific plot aesthetics
    min.segment.length = 0.1 # Min length of the segment line
  ) + 
  scale_color_manual(values = c("TRUE" = "red", "FALSE" = "blue")) +  # Colors for significant or not
  labs(title = "Chi2 Test P-values for Each Officer", x = "Badge ID", y = "P-value") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))  # Adjust x-axis labels for readability

# 2. cluster analysis: k-means clustering
race_profiles <- dcast(officer_race_counts, badge_id ~ subject_race, value.var = "count", fill = 0)

clusters <- kmeans(scale(race_profiles[,-1]), centers = 5)

race_profiles$cluster <- clusters$cluster

# calculate centroids of each cluster
centroids <- race_profiles %>%
  group_by(cluster) %>%
  summarise(centroid_White = mean(White), centroid_Black = mean(Black))

# Join centroids back to the main data
race_profiles <- race_profiles %>%
  left_join(centroids, by = "cluster")

# Calculate distance from centroid
race_profiles <- race_profiles %>%
  mutate(distance = sqrt((White - centroid_White)^2 + (Black - centroid_Black)^2))

# Determine outliers - customize threshold as needed
threshold <- mean(race_profiles$distance) + sd(race_profiles$distance) * 2
race_profiles <- race_profiles %>%
  mutate(outlier = ifelse(distance > threshold, "Outlier", "Normal"))

# Plotting with enhancements
ggplot(race_profiles, aes(x = White, y = Black, color = factor(cluster))) +
  geom_point(aes(size = distance, shape = outlier), alpha = 0.7) +
  scale_size_continuous(range = c(2, 8)) +  # Adjust size range as appropriate
  scale_shape_manual(values = c("Normal" = 16, "Outlier" = 4)) +
  labs(title = "Cluster of Officer Race Reporting Patterns",
       x = "Number of Times 'White' Reported", 
       y = "Number of Times 'Black' Reported") +
  theme_minimal() +
  theme(legend.position = "right")

# 3. machine learning: Logistic Regression Residuals
traffic_stops$subject_race <- as.factor(traffic_stops$subject_race)
model <- train(subject_race ~ time_of_day + subject_age + subject_gender + division, data = traffic_stops, method = "multinom", trControl = trainControl(method = "none"))
predicted_prob <- predict(model, traffic_stops, type = "prob")
residuals <- rowSums((model.matrix(~ subject_race - 1, traffic_stops) - predicted_prob)^2)

# Visualizing residuals
ggplot(traffic_stops, aes(x = badge_id, y = residuals)) +
  geom_jitter(alpha = 0.4) +
  labs(title = "Residuals from logistic regression by officer", x = "Badge ID", y = "Residuals")
```