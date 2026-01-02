# Automobile Sales & Service Analysis (Python EDA)

## Overview

This project analyzes **automobile sales and service data** using Python to uncover patterns in revenue, customer issues, and dealer performance. It combines exploratory data analysis (EDA), visualizations, and business insights to support decisions around **sales strategy, service quality, and dealer management**.  

The analysis is implemented in a Jupyter/Colab notebook using `pandas`, `numpy`, `matplotlib`, and `seaborn`. 

---

## Data

Two cleaned CSVs are used:

1. `automobile_sales_clean.csv`  
   - `Sale_ID`, `Date`, `Region`, `Dealer_Name`, `Vehicle_Model`, `Brand`  
   - `Price`, `Quantity`, `Sale_Type` (New / Used)  
   - Engineered: `Year`, `Month`, `Quarter`, `Vehicle_Age`, `Revenue`  

2. `automobile_service_clean.csv`  
   - `Service_ID`, `Date`, `Region`, `Dealer_Name`, `Vehicle_Model`  
   - `Issue_Type`, `Cost`, `Resolution_Time_Hrs`, `Customer_Satisfaction`  
   - Engineered: `Year`, `Month`, `Quarter`, `High_Satisfaction` flag  

These files simulate a year of automobile dealership activity across multiple regions and dealers.
---

## Tech stack

- **Python**  
- **pandas**, **numpy** for data manipulation  
- **matplotlib**, **seaborn** for visualization  
- Environment: Google Colab / Jupyter Notebook 

---

## Analysis steps

### 1. Data loading and cleaning

- Loaded `automobile_sales_clean.csv` and `automobile_service_clean.csv`.  
- Parsed `Date` columns and created `Year`, `Month`, and `Quarter`.  
- Created `Revenue = Price * Quantity` in the sales data.  
- Created `High_Satisfaction` binary flag (`Customer_Satisfaction >= 4`) in the service data. 

### 2. Sales EDA

Key visuals:

- **Monthly Revenue Trend**  
  - Line plot of monthly revenue using `Month_Period` vs `Revenue`.  
- **Revenue by Region**  
  - Bar chart grouping revenue by `Region`.  
- **Revenue by Brand**  
  - Bar chart grouping revenue by `Brand`.  
- **Revenue by Dealer**  
  - Bar chart comparing total `Revenue` per `Dealer_Name`.  
- **Distributions**  
  - Histograms of `Price` and counts of `Quantity` per transaction.

These plots show how revenue is distributed across time, geography, brands, and dealers.

### 3. Service EDA

Key visuals:

- **Issue Type Frequency**  
  - Bar chart of `Issue_Type` counts to identify the most common problems.  
- **Resolution Time vs Satisfaction**  
  - Scatter plot of `Resolution_Time_Hrs` vs `Customer_Satisfaction`.  
- **Satisfaction Distribution**  
  - Countplot of satisfaction ratings (1–5).  
- **Average Satisfaction by Dealer**  
  - Bar chart of mean `Customer_Satisfaction` per `Dealer_Name`.  
- **Service Cost and Resolution Time Distributions**  
  - Histograms for `Cost` and `Resolution_Time_Hrs`. 

### 4. Combined dealer view

- Built a **dealer_summary** table by combining:
  - Total sales revenue per dealer from the sales dataset.  
  - Average customer satisfaction per dealer from the service dataset.  
- Created a scatter plot: `Total_Revenue` vs `Avg_Satisfaction`, with dealer labels. 

This highlights which dealers are strong on both sales and service, and which ones are unbalanced.

---

## Key findings & conclusions

From the notebook outputs (tables + plots), the following conclusions can be stated:

### Sales side

- **Seasonality in revenue**  
  - Monthly revenue is not uniform across the year; some months clearly outperform others, indicating **seasonal/occasion-driven demand**. This can be used to plan stock and campaigns ahead of high-demand periods.

- **Regional performance differences**  
  - Some regions contribute noticeably more to total revenue than others. These should be treated as **priority markets** for inventory, marketing, and new model launches, while low-performing regions may need localized offers or dealer support. 

- **Brand mix impact**  
  - Certain brands consistently generate higher revenue, reflecting strong product–market fit and demand. These brands should be central to **promotional strategy and showroom focus**, while underperforming brands might need repositioning or targeted incentives. 

- **Dealer revenue concentration**  
  - Revenue is concentrated among a few dealers, making them **key accounts**. Their practices (lead handling, local marketing, test drive process) can be benchmarked and replicated, while lower-revenue dealers might require training or territory review. 

### Service side

- **Few issue types dominate complaints**  
  - A small set of issues (e.g., transmission, engine, brakes, AC, depending on your exact counts) accounts for most service cases, indicating clear focus areas for **technical training, parts stocking, and OEM quality feedback**. 

- **Resolution time and satisfaction**  
  - The scatter plot shows that very high resolution times tend to be associated with lower satisfaction, confirming that **fast and predictable service** is important, even if the relationship isn’t perfectly linear. 

- **Dealer-level satisfaction differences**  
  - Some dealers maintain higher average satisfaction scores, while others lag. High-scoring dealers can be treated as **best-practice references**, and low-scoring ones should be prioritized for **process audits, staff coaching, or resource strengthening**. 

### Combined dealer view (sales + service)

- **High-sales + high-satisfaction dealers**  
  - These dealers are ideal and should be **protected and rewarded**; they represent both strong revenue and good customer experience. 

- **High-sales + low-satisfaction dealers**  
  - This group is strategically critical: they bring in revenue but risk churn and reputational damage. They should be first in line for **service quality improvement programs and closer monitoring**. [file:65]

- **Low-sales dealers**  
  - Depending on satisfaction levels, these are either growth opportunities (if satisfaction is good) or potential exits (if both sales and satisfaction are weak).

---

## How to run

1. Upload `automobile_sales_clean.csv` and `automobile_service_clean.csv` into your Colab or Jupyter environment.  
2. Open the notebook (e.g., `Automobile_sales_and_service_analysis.ipynb`).
3. Install dependencies if needed:
pip install pandas numpy matplotlib seaborn
4. Run all cells to reproduce the EDA, plots, and summary tables.

---
