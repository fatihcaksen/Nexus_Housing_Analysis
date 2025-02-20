# Nexus Housing Data Analysis

## 📌 Project Overview
This project provides an in-depth analysis of **housing placement efficiency and membership engagement** using data-driven insights. The goal is to evaluate key performance metrics and identify areas for operational improvements. 

Housing access is a critical issue, and this analysis seeks to enhance decision-making by leveraging data analytics to understand trends in participant referrals, unit viewings, and borough-specific challenges. 

The primary objectives of this project are:
- **Assessing the time it takes for participants to schedule their first unit viewing**
- **Identifying borough-wise variations in completed unit viewings**
- **Analyzing the impact of missing referral dates on the efficiency of the housing process**
- **Developing data-driven strategies to improve housing accessibility**

By analyzing patterns and trends, this report offers actionable recommendations to optimize membership engagement and operational strategies.

---

## 📊 Key Insights from the Analysis
### **1️⃣ Time-to-First Completed Viewing**
- **Average time to first completed unit viewing:** 89 days
- This waiting period suggests delays in scheduling, limited unit availability, or inefficient administrative processing.
- **Potential Solutions:**
  - Implement automated scheduling and follow-up reminders using CRM systems (e.g., Salesforce).
  - Prioritize high-need cases for faster processing.
  - Introduce real-time dashboards to track pending applications.

### **2️⃣ Completed Unit Viewings by Borough**
| **Borough**       | **Completed Viewings** |
|-------------------|----------------------|
| Bronx            | 217  |
| Brooklyn         | 123  |
| Manhattan        | 129  |
| Queens          | 146  |
| Staten Island    | 27   |

- The Bronx had the highest number of completed viewings, which may indicate a larger stock of available units or higher demand.
- Staten Island had significantly fewer completed viewings, possibly due to:
  - Lower participant interest in the area.
  - Fewer housing options available.
  - Logistical challenges in transportation and accessibility.
- **Potential Solutions:**
  - Conduct borough-specific outreach programs to better match participants with available units.
  - Evaluate the distribution of unit availability to ensure equitable housing opportunities.

### **3️⃣ Referral Date Missing Values**
- The dataset contained **3 missing referral dates** for participants.
- Missing referral dates impact time-to-first viewing calculations and delay tracking.
- **Data Quality Fixes:**
  - Implement validation checks to ensure referral dates are mandatory in future data entries.
  - Automate data correction processes within CRM systems to flag and resolve missing values.

### **4️⃣ Conversion Rate Analysis**
- **Metric:** Percentage of participants who proceed with a unit after viewing.
- If conversion rates are low, possible causes include:
  - Poor unit conditions.
  - Mismatch in location preferences.
  - Affordability concerns.
- **Solutions:**
  - Improve pre-viewing screening to align participant preferences with unit availability.
  - Work with landlords to improve unit conditions.
  - Provide better communication regarding unit features before viewings.

---

## ⚙️ Tools & Technologies Used
- **Programming & Analytics:**
  - R (RMarkdown, ggplot2, dplyr, readr, leaflet, plotly)
  - Power BI for interactive dashboards
  - SQL for structured data querying
  - Excel for initial data cleaning and transformation

- **Business Intelligence & Membership Analytics:**
  - CRM systems for tracking member engagement
  - Trend analysis for identifying historical patterns
  - Geospatial analysis using Leaflet for unit distribution

- **Visualization Techniques:**
  - Time series plots to track participant engagement over time
  - Bar charts to highlight borough-wise disparities
  - Interactive maps to display unit distributions

---

## 🏆 Why This Project Matters
This project is designed to demonstrate:
✔ **Expertise in Data Analytics** – Cleaning, analyzing & visualizing data to drive business insights.  
✔ **Strategic Decision-Making** – Identifying patterns to optimize engagement & operational efficiency.  
✔ **Technical Acumen** – Leveraging tools such as Power BI, Excel, and R for data-driven solutions.  

By focusing on housing placements and membership engagement, this project offers **scalable solutions** that can be applied across similar operational challenges in different organizations.

---

## 🚀 How to Run This Analysis
1. Clone the repository:  
   ```bash
   git clone https://github.com/fatihcaksen/Nexus_Housing_Analysis.git
   ```
2. Open **RStudio** and run the `.Rmd` file.
3. Ensure datasets are placed in:
   ```
   C:/Users/Fatih/Desktop/Github_blog/Nexus_Housing_Analysis/data/
   ```
4. Render the **HTML report** using RMarkdown.

---

📌 **Data-driven decision-making can transform housing accessibility. Let's build more efficient systems together!**
