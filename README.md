# Automobile Sales & Service Analytics 

## Project overview

This project is an end-to-end **data science** case study on automobile **sales and service** data from multiple brands, regions, and dealers across 2023.

It starts with exploratory data analysis (EDA) and then builds:

- A **sales price prediction** model (regression).
- A **customer satisfaction prediction** model (classification).
- A **dealer segmentation** module using clustering.

The goal is to show how raw transactional data can be turned into **actionable insights** for pricing, service quality improvement, and dealer performance management.[3][2][1]

***

## Data description

Two main CSV files are used:

1. **Sales dataset – `automobile_sales-1.csv`**
   - `Sale_ID`: Unique sale identifier  
   - `Date`: Date of sale  
   - `Region`: North, South, East, West  
   - `Dealer_Name`: Name of dealer (e.g., Elite Motors, DriveNow)  
   - `Vehicle_Model`: Model name (e.g., Sonet, Nexon, Creta)  
   - `Brand`: Brand (Kia, Tata, Hyundai, Maruti)  
   - `Price`: Transaction price (₹)  
   - `Quantity`: Units sold in that transaction  
   - `Sale_Type`: New vs Used  

2. **Service dataset – `automobile_service-1.csv`**
   - `Service_ID`: Unique service case identifier  
   - `Date`: Service date  
   - `Region`: Service region  
   - `Dealer_Name`: Dealer handling service  
   - `Vehicle_Model`: Vehicle model  
   - `Issue_Type`: Complaint type (Transmission Lag, Engine Overheat, Brake Issue, AC Failure, Battery Fault, Suspension Problem, etc.)  
   - `Cost`: Service cost (₹)  
   - `Resolution_Time_Hrs`: Time taken to resolve (hours)  
   - `Customer_Satisfaction`: Rating from 1 to 5  

Additional time-based features are engineered from `Date`: `Year`, `Month`, and `Quarter`. 

***

## Exploratory data analysis (EDA)

Key EDA steps and insights:

- **Monthly sales revenue**  
  - Aggregated sales by month to visualize revenue trends.  
  - Observed clear fluctuations, with peaks around festive months and dips in some off-season months.

- **Sales by region**  
  - East and South regions contributed a large share of total revenue.  
  - This points to stronger market presence and demand in those geographies.

- **Sales by brand**  
  - Brands like **Kia** and **Tata** emerged as top performers in total revenue.  
  - Hyundai and Maruti followed, indicating competitive but slightly lower sales volumes in this sample.

- **Most common service issues**  
  - The most frequent issues were **Transmission Lag**, **Engine Overheat**, and **Brake Issue**, followed by AC Failure, Battery Fault, and Suspension Problem.[2][3]
  - These categories highlight where manufacturers and service centers should focus on quality and spare-part planning.

- **Resolution time vs customer satisfaction**  
  - Plotted `Resolution_Time_Hrs` vs `Customer_Satisfaction`.  
  - No perfect linear relationship, indicating that satisfaction depends on issue type, expectations, and communication, not just speed.

***

## Sales price prediction (regression)

### Problem

Predict the **transaction price** of a sale using attributes such as brand, region, dealer, sale type, and calendar features. This can support **pricing strategies**, promotion planning, and revenue forecasting.

### Features

From the augmented sales dataset (`automobile_sales_augmented.csv`): 

- **Target**: `Price`  
- **Features**:  
  - Categorical: `Brand`, `Region`, `Dealer_Name`, `Sale_Type`  
  - Numerical: `Month`, `Quarter`, `Quantity`, `Vehicle_Age` (proxy)  

Categorical variables are one-hot encoded and numerical features are standardized using a `ColumnTransformer`. 

### Models

- **Ridge Regression** as a linear baseline.   
- **Random Forest Regressor** to capture nonlinear interactions.   

Both are implemented via scikit-learn Pipelines (preprocessing + model). 

### Data augmentation

Because the original dataset has only ~100 rows, a **segment-based synthetic generator** was used:

- Defined segments as `Brand + Sale_Type`.  
- Computed average price per segment.  
- Generated ~20× more rows by bootstrapping base rows and sampling prices around the segment mean with controlled noise, plus small perturbations in month and date.   

This creates a larger, realistic dataset for training and evaluation.

### Performance (augmented)

On the augmented dataset:

- **Ridge Regression (augmented)**  
  - RMSE ≈ **₹1,14,388**  
  - R² ≈ **0.18**

- **Random Forest Regressor (augmented)**  
  - RMSE ≈ **₹1,12,451**  
  - R² ≈ **0.21**

These results show that, with more data and stable segment patterns, the model captures a meaningful portion of price variance and can be further improved with richer features (e.g., vehicle age, mileage, discount info).

***

## Service satisfaction prediction (classification)

### Problem

Predict whether a service case will result in **high customer satisfaction** (rating ≥ 4) based on issue type, resolution time, dealer, region, and vehicle model. This enables **proactive handling of risky cases**.

### Features

From the augmented service dataset (`automobile_service_augmented.csv`): 

- **Target**: `High_Satisfaction` (1 if rating ≥ 4, else 0)  
- **Features**:  
  - Categorical: `Dealer_Name`, `Issue_Type`, `Region`, `Vehicle_Model`, `Month`, `Quarter`  
  - Numerical: `Resolution_Time_Hrs`  

### Data augmentation

Similar to sales, a realistic augmentation was used:[2]

- Defined segments as `Issue_Type + High_Satisfaction`.  
- For each segment, estimated typical resolution time and satisfaction.  
- Generated ~20× more rows by sampling new cases around these segment patterns, with slight noise in resolution time and satisfaction, and small date shifts.   

This preserves the relationship between issue type, resolution behavior, and satisfaction while increasing sample size.

### Models

- **Logistic Regression** baseline.   
- **Random Forest Classifier**, tuned with more trees and limited depth. 

### Performance (augmented)

On the augmented dataset (test size ~400 rows):

- **Logistic Regression (augmented)**  
  - Accuracy ≈ **0.74**  
  - Macro F1 ≈ **0.72**

- **Random Forest Classifier (augmented)**  
  - Accuracy ≈ **0.84**  
  - Macro precision ≈ **0.83**, macro recall ≈ **0.83**, macro F1 ≈ **0.83**
  - Class 0 (Low satisfaction): precision ≈ 0.85, recall ≈ 0.87  
  - Class 1 (High satisfaction): precision ≈ 0.82, recall ≈ 0.79

This shows that service attributes, especially **issue type and resolution time**, are strong predictors of satisfaction, and the model can be used to flag at-risk tickets early.

***

## Dealer segmentation (clustering)

### Approach

To understand dealer performance holistically, sales and service metrics are aggregated at dealer level: 

- From sales:  
  - `Total_Sales_Value`, `Num_Sales` per `Dealer_Name`  
- From service:  
  - `Avg_Satisfaction`, `Avg_Resolution_Time`, `Num_Service_Cases` per `Dealer_Name`  

These features are standardized and clustered using **KMeans (k=3)**. 

### Insights

Example results for main dealers:

- **Elite Motors**: Highest total sales and highest average satisfaction, moderate resolution times → **benchmark dealer**.  
- **DriveNow & Metro Cars**: Strong sales but lower satisfaction → **high-revenue but at-risk segment** that needs service quality improvement.  
- **SpeedX**: Decent sales and mid-level satisfaction, but relatively higher resolution times → operational inefficiency opportunity.  
- **Wheels Auto**: Stable sales and above-average satisfaction, close to the better cluster.  

The cluster scatterplot (Total_Sales_Value vs Avg_Satisfaction) clearly separates these groups and guides where to focus training, incentives, and process improvements.[3]

***

## Tech stack

- **Language**: Python  
- **Data analysis**: pandas, NumPy  
- **Visualization**: Matplotlib, Seaborn  
- **Machine learning**: scikit-learn (Pipelines, ColumnTransformer, Ridge, Logistic Regression, Random Forest, KMeans)  
- **Environment**: Google Colab (free tier)  

***

## How to run

1. Upload `automobile_sales_clean.csv` and `automobile_service_clean.csv` to your Colab environment.[
2. Paste the notebook code (imports, EDA, augmentation, models, clustering) into a Colab notebook.  
3. Adjust file paths if required (e.g., `/content/drive/MyDrive/...` for Google Drive).  
4. Run all cells to reproduce EDA visuals, model metrics, and clustering plots. 

---

## Possible extensions

- Add features like `Vehicle_Age`, mileage, discount %, and financing details if available.  
- Try gradient boosting models (e.g., XGBoost, LightGBM) for both regression and classification.  
- Build a simple **dashboard** (Streamlit or Power BI) to visualize KPIs and model outputs.  
- Implement **cross-validation** and **hyperparameter tuning** (GridSearchCV/RandomizedSearchCV) for more robust model selection.  

***
