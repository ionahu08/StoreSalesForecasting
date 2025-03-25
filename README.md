# Predict Future Sales - Kaggle Competition

## Project Overview
This project aims to forecast the total number of products sold in each shop for November 2015, using historical daily sales data from January 2013 to October 2015. The competition dataset is provided by Kaggle: [Predict Future Sales](https://www.kaggle.com/competitions/competitive-data-science-predict-future-sales/data).

## Dataset Description
The dataset consists of multiple files:
- `sales_train.csv`: Training data with 2,935,849 rows of daily sales records (January 2013 - October 2015).
- `test.csv`: Test data with 214,200 rows, for which sales predictions need to be made.
- `sample_submission.csv`: A sample submission file.
- `items.csv`: Information about items/products.
- `item_categories.csv`: Information about item categories.
- `shops.csv`: Information about shops.

### Key Data Fields
- `ID`: Identifier for (shop, item) pairs in the test set.
- `shop_id`: Unique identifier for a shop.
- `item_id`: Unique identifier for a product.
- `item_category_id`: Unique identifier for an item category.
- `item_cnt_day`: Number of products sold (target variable at the monthly level).
- `item_price`: Current price of an item.
- `date`: Date in `dd/mm/yyyy` format.
- `date_block_num`: A continuous month index (January 2013 = 0, February 2013 = 1, ..., October 2015 = 33).
- `item_name`: Name of the item.
- `shop_name`: Name of the shop.
- `item_category_name`: Name of the item category.

## Jupyter Notebooks

### 01_TimeSeries.ipynb

#### **Program Overview**
This notebook is designed to analyze the seasonality, trends, and overall patterns in sales data using time series analysis. The goal is to analyze historical sales data to extract meaningful insights and forecast future sales.

#### **Step 1: Data Preprocessing & Feature Engineering**
- Convert `date` column to `yyyy-mm-dd` format.
- Aggregate monthly data by \((shop\_id, item\_id, date\_block\_num)\):
  - `date_min`, `date_max`
  - `item_price_mean`, `item_cnt_day_sum`
- Visualize the distribution of items across categories and plot the top 10 categories.

#### **Step 2: Time Series Analysis**
- Aggregate `item_cnt_day` at the monthly level and plot the total sales.
- Compute and visualize rolling mean and standard deviation (window size = 12 months).
- Decompose time series using both multiplicative and additive models.
- Perform an Augmented Dickey-Fuller (ADF) test to check stationarity:
  - Initial \( p \)-value = 0.143 (non-stationary).
  - Apply de-trending and de-seasonalization, then check for stationarity again.
- Perform grid search to optimize ARIMA model parameters based on AIC.
- Train ARIMA(1,1) and predict total sales for the next month (all items & shops).

### 02_Models.ipynb

#### **Program Overview**
This notebook focuses on building machine learning models to predict future sales. It includes data preprocessing, feature engineering, and training several models for sales prediction.

#### **Step 1: Data Preprocessing & Feature Engineering**
- Handle outliers in `item_price` and `item_cnt_day`.
- Filter `shop_id` values to match those in the test set.
- Extract `city` from `shop_name` and apply Label Encoding.
- Remove redundant text features (`shop_name`, `item_name`).
- Create a `first_sale_date` feature representing the first sale date for each product.
- Extract category from `item_category_name` and apply Label Encoding, replacing rare categories with "etc."
- Construct a grid of all \((date\_block\_num, shop\_id, item\_id)\) combinations.
- Aggregate sales at the monthly level and compute derived features for mean sales per `item_id`, `city`, `shop_id-item_category_id`.

#### **Step 2: Lag Features**
- Generate 3-month lag features for:
  - `item_cnt_month`
  - `item_count`
  - `item_price_mean`
- Create lag-based rolling statistics and remove redundant features.
- Remove data for `date_block_num < 3` due to unavailable lag features.

#### **Step 3: Model Training & Evaluation**

| Model       | Training RMSE | Validation RMSE | Final Prediction RMSE |
|-------------|---------------|-----------------|-----------------------|
| LightGBM    | 0.799125      | 0.794661        | 0.88207               |
| CatBoost    | 0.857217      | 0.804536        | 0.91695               |
| XGBoost     | 0.826145      | 0.796617        | 0.91145               |
| RandomForest| 0.905847      | 0.836250        | 0.96053               |

## Conclusion
- Time series analysis revealed seasonality and trends in sales data.
- Among machine learning models, LightGBM achieved the best performance.
