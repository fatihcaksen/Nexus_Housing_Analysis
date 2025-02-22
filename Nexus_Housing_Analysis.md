
---
title: "Nexus Housing Data Analysis"
author: "Fatih Caksen"
output:
  github_document: default
  rmarkdown::github_document: default
---


```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE, warning = FALSE, message = FALSE)
library(dplyr)
library(ggplot2)
library(readr)
library(leaflet)
library(lubridate)
library(sf)
library(tibble)
library(plotly)
```

# **1. Data Quality Assessment**

## **Introduction**
High-quality data is the foundation of accurate analysis and decision-making. Any inconsistencies or missing data can lead to flawed insights. This section evaluates the dataset's **completeness, accuracy, and consistency**.


# --------------------------------------
## **Steps Taken for Data Quality Check**

### **1. Checking for Missing Values**
Missing values can create significant problems when calculating metrics. Here, we check for missing values in each dataset and assess their impact.

```{r missing-values}
# Load CSV files
unit_viewings <- read_csv("C:/Users/Fatih/Desktop/Github_blog/Nexus_Housing_Analysis/data/unit_viewings.csv")
units <- read_csv("C:/Users/Fatih/Desktop/Github_blog/Nexus_Housing_Analysis/data/units.csv")
participants <- read_csv("C:/Users/Fatih/Desktop/Github_blog/Nexus_Housing_Analysis/data/participants.csv")

# Convert date columns to Date format
unit_viewings$unit_viewing_date <- as.Date(unit_viewings$unit_viewing_date)
participants$participant_referral_date <- as.Date(participants$participant_referral_date)

# Count missing values
missing_values <- data.frame(
  Dataset = c("Unit Viewings", "Units", "Participants"),
  Missing_Values = c(sum(is.na(unit_viewings)), sum(is.na(units)), sum(is.na(participants)))
)
missing_values
```

### **Findings and Impact:**
- **Participants dataset contains missing referral dates**, affecting the ability to measure referral efficiency.
- **The unit viewings and units datasets have minimal missing data**, ensuring reliability in these datasets.

### **2. Checking for Duplicates**
Duplicate entries can lead to overestimation in analysis. Here, we check for duplicate records.

```{r duplicates}
duplicate_counts <- data.frame(
  Dataset = c("Unit Viewings", "Units", "Participants"),
  Duplicates = c(sum(duplicated(unit_viewings)), sum(duplicated(units)), sum(duplicated(participants)))
)
duplicate_counts
```

### **Findings:**
- No duplicate records were found, meaning the dataset is free from redundancy.

---

# **2. Total Completed Unit Viewings by Borough**

## **Why This Matters?**
Analyzing the number of completed viewings per borough helps us understand where most referrals convert into successful unit viewings. This data is **essential for identifying borough-specific trends, demand distribution, and potential accessibility issues**.

## **Analysis Approach**
- **Filter for completed unit viewings**.
- **Join viewing data with borough information** from the units dataset.
- **Group by borough** and calculate the count of completed viewings.

```{r completed-viewings}
completed_viewings <- unit_viewings %>% 
  filter(unit_viewing_status == "completed") %>%
  left_join(units, by = "unit_id") %>%
  group_by(borough) %>%
  summarise(completed_viewings = n())

ggplot(completed_viewings, aes(x = borough, y = completed_viewings, fill = borough)) +
  geom_bar(stat = "identity") +
  theme_minimal() +
  labs(title = "Completed Unit Viewings by Borough", x = "Borough", y = "Completed Viewings") +
  theme(legend.position = "none")
```

### **Findings and Implications:**
- **The Bronx has the highest number of completed viewings**, indicating high demand or a well-functioning referral process.
- **Staten Island has the lowest number of completed viewings**, suggesting limited housing availability or lower participant interest.

---

# **3. Time to First Completed Viewing**

## **Why This Matters?**
Tracking the time from referral to the first completed unit viewing helps measure **program efficiency**. A shorter time frame suggests an **effective referral process**, while longer delays may indicate **bottlenecks or inefficiencies**.

## **Analysis Approach**
- **Merge the unit viewings dataset with participant referral dates**.
- **Calculate the time difference** between referral and the first completed viewing.
- **Compute the average wait time for first viewings**.

```{r time-to-first-viewing}
first_viewing_time <- unit_viewings %>% 
  filter(unit_viewing_status == "completed") %>%
  left_join(participants, by = "participant_id") %>%
  group_by(participant_id) %>%
  summarise(first_viewing = min(unit_viewing_date, na.rm = TRUE),
            referral_date = first(participant_referral_date)) %>%
  mutate(days_to_first_viewing = as.numeric(first_viewing - referral_date))

average_days_to_first_viewing <- mean(first_viewing_time$days_to_first_viewing, na.rm = TRUE)
average_days_to_first_viewing

ggplot(first_viewing_time, aes(x = days_to_first_viewing)) +
  geom_histogram(binwidth = 10, fill = "blue", color = "black", alpha = 0.7) +
  theme_minimal() +
  labs(title = "Distribution of Time to First Viewing", x = "Days", y = "Number of Participants")
```

### **Findings and Recommendations:**
- The **average wait time is** `89 days for viewing*.
- **Recommendation:** Implement **automated follow-ups and scheduling improvements** to reduce wait times.

---

# **4. Heatmap of Completed Viewings Over Time**

## **Why This Matters?**
A heatmap highlights **seasonal fluctuations** in completed unit viewings, allowing for **better planning and resource allocation**.

```{r heatmap-visualization}
unit_viewings %>%
  filter(unit_viewing_status == "completed") %>%
  left_join(units, by = "unit_id") %>%
  mutate(month_year = floor_date(unit_viewing_date, "month")) %>%
  group_by(borough, month_year) %>%
  summarise(completed_viewings = n(), .groups = "drop") -> heatmap_data

ggplot(heatmap_data, aes(x = month_year, y = borough, fill = completed_viewings)) +
  geom_tile() +
  scale_fill_gradient(low = "lightyellow", high = "red") +
  theme_minimal() +
  labs(title = "Completed Unit Viewings Heatmap", x = "Month", y = "Borough", fill = "Completed Viewings") +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
```

---

# **5. Interactive Scatter Plot for Referral to First Viewing**
```{r scatter-plot-interactive}
ggplotly(ggplot(first_viewing_time, aes(x = referral_date, y = days_to_first_viewing, color = days_to_first_viewing)) +
  geom_point(alpha = 0.7, size = 3) +
  theme_minimal() +
  labs(title = "Time from Referral to First Completed Viewing", x = "Referral Date", y = "Days to First Viewing"))
```

---

```{r city-map-data}
borough_coordinates <- tribble(
  ~borough, ~latitude, ~longitude,
  "Bronx", 40.8448, -73.8648,
  "Brooklyn", 40.6782, -73.9442,
  "Manhattan", 40.7831, -73.9712,
  "Queens", 40.7282, -73.7949,
  "Staten Island", 40.5795, -74.1502
)

completed_viewings_map <- unit_viewings %>% 
  filter(unit_viewing_status == "completed") %>%
  left_join(units, by = "unit_id") %>%
  group_by(borough) %>%
  summarise(completed_viewings = n(), .groups = "drop") %>%
  left_join(borough_coordinates, by = "borough")
```


# **6. Interactive City Map of Completed Viewings**
```{r city-map-interactive}
leaflet(completed_viewings_map) %>% 
  addTiles() %>% 
  addCircleMarkers(lng = ~longitude, lat = ~latitude, color = "blue", 
                   radius = ~sqrt(completed_viewings) * 2, opacity = 0.7, fillOpacity = 0.5,
                   popup = ~paste("Borough:", borough, "<br>Completed Viewings:", completed_viewings)) %>%
  addLegend("bottomright", colors = "blue", labels = "Completed Viewings", title = "Legend")
```

---

# **Conclusion and Recommendations**
This analysis reveals **efficiency gaps in referrals, borough-specific disparities, and seasonal trends**. Key recommendations include:
1. **Reducing wait times through automated scheduling.**
2. **Addressing borough-specific disparities in housing supply.**
3. **Leveraging seasonal insights for optimized housing placements.**

---
