# Demand Forecasting and Inventory Optimization using Python - Time Series Data - Data Science Project

## Table of Contents

- [Introduction](#introduction)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
- [Usage](#usage)
  - [Data Preparation](#data-preparation)
  - [Demand Forecasting](#demand-forecasting)
  - [Inventory Optimization](#inventory-optimization)
- [Time Series Analysis](#time-series-analysis)
- [Why SARIMA](#why-sarima)
- [Dataset](#dataset)
- [Results](#results)
- [Contributing](#contributing)
- [License](#license)

## Introduction

This project aims to forecast demand using time series analysis and optimize inventory management based on the forecasted demand.

## Getting Started

### Prerequisites

To run this project, you'll need the following Python libraries installed:

- numpy
- pandas
- matplotlib
- plotly
- statsmodels

You can install these libraries using pip:

```bash
pip install -r requirements.txt
```

### Installation

1. Clone this repository to your local machine:

```bash
git clone https://github.com/AyushMehta1702/Demand-Forecasting-and-Inventory-Optimization-using-Python---Time-Series.git
```

2. Navigate to the project directory:

```bash
cd Demand-Forecasting-and-Inventory-Optimization-using-Python---Time-Series
```

## Usage

### Data Preparation

Before running the code, ensure you have a CSV file named `demand_inventory.csv` with columns 'Date', 'Demand', and 'Inventory'.

### Demand Forecasting

Run the code to perform demand forecasting using SARIMA. Adjust the SARIMA order and seasonal_order parameters as needed for your dataset.

```python
order = (1, 1, 1,)
seasonal_order = (1, 1, 1, 2) # 2 because here data contains only two months of periods 

model = SARIMAX(time_series, order=order, seasonal_order=seasonal_order)
model_fit = model.fit(disp=False)

future_steps = 10

prediction = model_fit.predict(len(time_series), len(time_series)+ future_steps -1)
prediction = prediction.astype(int)
print(prediction)
```

### Inventory Optimization

Calculate the optimal order quantity, reorder point, safety stock, and total cost using the Newsvendor formula. Adjust parameters like lead time, service level, holding cost, and stockout cost to match your business requirements.

```python
# Inventory Optimization

future_dates = pd.date_range(start=time_series.index[-1] + pd.DateOffset(days=1), periods=future_steps, freq='D')

forecast_demand = pd.Series(prediction, index=future_dates)

initial_inventory = 5500

lead_time = 1 # No. of day it takes to replenish inventory. its diff for every business. here 1 is an example.

service_level = 0.95 # probability of not stocking out. its diff for every business. 0.95 is an example.

# Calculating the optimal order quantity using the Newsvendor formula

z = np.abs(np.percentile(forecast_demand, 100 * (1 -service_level)))
order_quantity = np.ceil(forecast_demand.mean() + z).astype(int)

reorder_point = forecast_demand.mean() * lead_time + z

safety_stock = reorder_point - forecast_demand.mean() * lead_time

# calculating total cost (holding cost + stockout cost)
holding_cost = 0.1
stockout_cost = 10

total_h_cost = holding_cost * (initial_inventory + 0.5 * order_quantity)
total_s_cost = stockout_cost * np.maximum(0, forecast_demand.mean() * lead_time - initial_inventory)

total_cost = total_h_cost + total_s_cost 

```

## Time Series Analysis

Here is a brief explanation of different time series analysis models:

| Model   | Explanation                                       |
| ------- | ------------------------------------------------- |
| ARMA    | Autoregressive Moving Average model. A combination of autoregressive and moving average components used for time series forecasting. It doesn't account for seasonality. |
| ARIMA   | Autoregressive Integrated Moving Average model. Extends ARMA by including differencing to make the time series stationary. Suitable for non-seasonal data. |
| SARIMA  | Seasonal Autoregressive Integrated Moving Average model. Extends ARIMA to include seasonality in the model. Suitable for seasonal time series data. |

## Why SARIMA

SARIMA (Seasonal Autoregressive Integrated Moving Average) is selected for this project because it is well-suited for time series data with seasonal patterns, which is often the case in demand forecasting scenarios. SARIMA incorporates autoregressive, differencing, and moving average components, making it effective in capturing both short-term and long-term dependencies in the data. By considering seasonality, SARIMA can provide more accurate demand forecasts, making it a suitable choice for inventory optimization. Additionally, SARIMA models can handle various types of seasonal patterns, making them versatile for different business contexts.

## Dataset

The dataset used in this project is the "Inventory Optimization: Case Study" dataset, which is available at [Inventory Optimization: Case Study](https://statso.io/inventory-optimization-case-study/).

This dataset contains historical data related to demand and inventory, making it suitable for demand forecasting and inventory optimization tasks. The dataset includes information such as date, demand, and inventory levels, which are essential for understanding and optimizing inventory management.

## Results

The project will output the optimal order quantity, reorder point, safety stock, and total cost based on the provided parameters and the demand forecast.
```python
print("Optimal Order Quantity:", order_quantity)
print("\n Reorder Point:", reorder_point)
print("\n Safety Stock:", safety_stock)
print("\n Total Cost:", total_cost)
```
Optimal Order Quantity: 236

Reorder Point: 235.25

Safety Stock: 114.45

Total Cost: 561.8000000000001

## Contributing

If you would like to contribute to this project, please open an issue or create a pull request. I welcome contributions and improvements.

## License

This project is licensed under the [MIT License](LICENSE).
```
Feel free to copy and paste this code into the "Edit" tab of your README file on GitHub. Be sure to customize it further with any specific details or adjustments you'd like to make for your project.
```
## Credits
Credit goes to [AmanKharwal](https://github.com/amankharwal) and [Codebasics](https://github.com/codebasics) whose tutorials helped me a lot to learn and upgrade my skills in Data Science, Data Analysis, Machine Learning, and Deep Learning. Many thanks to them.
