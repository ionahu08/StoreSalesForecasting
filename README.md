# 01_TimeSeries

This notebook is designed to explore the seasonality, trend, and overall patterns in sales data using time series analysis. The goal is to analyze historical sales data and gain insights into its underlying structure.

## Program Overview

The project is structured as follows:

1. **Data Preprocessing and Exploratory Data Analysis (EDA)**
2. **Time Series Modeling using ARIMA**
3. **Evaluation and Forecasting**

## Steps

### 1. Import and Preprocess Data + EDA + Feature Engineering

#### 1.1 Data Preparation
- **Format Date Column**: Convert the date column from string format (`mm.dd.yyyy`) to `datetime` format (`yyyy-mm-dd`).
  
#### 1.2 Aggregating Sales Features (Monthly Level)
- Group the data by `shop_id`, `item_id`, and `date_block_num` to generate aggregated features such as:
  - `date_min`: Minimum date for each group.
  - `date_max`: Maximum date for each group.
  - `item_price_mean`: Average price of items in each group.
  - `item_cnt_day_sum`: Total number of items sold in each group.

#### 1.3 Item Category Analysis
- Calculate the number of items per category and visualize the top 10 categories with the most items.

### 2. Single Series: `item_cnt_day ~ date_block_num`

#### 2.1 Sales Aggregation
- Calculate the total sales for the entire company (all items from all shops) by summing `item_cnt_day` for each month.

#### 2.2 Visualization
- Plot the total sales data for visualization and analysis.

#### 2.3 Rolling Statistics
- **Rolling Mean**: Calculate the rolling mean (moving average) with a 12-month window to smooth the data and identify trends.
- **Rolling Standard Deviation**: Calculate the rolling standard deviation with a 12-month window to measure volatility.

#### 2.4 Decomposition
- Use the `seasonal_decompose` function from the `statsmodels` library to decompose the time series data into three components:
  - **Trend**: The long-term progression of the series.
  - **Seasonal**: Periodic fluctuations in the data.
  - **Residual**: The remaining noise in the data.
- Both multiplicative and additive models are used for decomposition.

#### 2.5 ADF Test
- Perform the Augmented Dickey-Fuller (ADF) test to check if the time series is stationary. In this case, the p-value is 0.143, which is greater than 0.05, indicating the series is **not stationary**.

#### 2.6 De-trending and Seasonality Removal
- Apply methods to de-trend and remove seasonality from the series. After this, the time series becomes stationary as confirmed by the ADF test.

### 3. Time Series Forecasting with ARIMA

#### 3.1 Grid Search for ARIMA Model
- Perform a grid search to find the optimal parameters for the ARIMA model using the **Akaike Information Criterion (AIC)**. The best ARIMA model is identified as ARIMA(1,1).

#### 3.2 Forecasting
- Use the ARIMA(1,1) model to predict the total sales for the next month (for all items and shops combined).
