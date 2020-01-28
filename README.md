
# Testing for Trends - Lab

## Introduction

In this lab, you'll practice your knowledge of testing for stationarity.

## Objectives

You will be able to:

- Use rolling statistics as a check for stationarity 
- Use the Dickey-Fuller test and conclude whether or not a dataset is exhibiting stationarity 

## Importing the data

Let's look at some new data. In this lab, we'll work with a time series in Python by using the popular [Air Passengers dataset](https://www.analyticsvidhya.com/wp-content/uploads/2016/02/AirPassengers.csv).

Start by running the cell below to import the necessary libraries. 


```python
# Import necessary libraries
import pandas as pd
import numpy as np
import matplotlib.pylab as plt
%matplotlib inline
```

The dataset is stored in `'passengers.csv'`. Import it and view the first five rows. 


```python
# Import 'passengers.csv'
data = pd.read_csv('passengers.csv')

# View the first five rows
data.head(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Month</th>
      <th>#Passengers</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>1949-01-01</td>
      <td>112</td>
    </tr>
    <tr>
      <td>1</td>
      <td>1949-02-01</td>
      <td>118</td>
    </tr>
    <tr>
      <td>2</td>
      <td>1949-03-01</td>
      <td>132</td>
    </tr>
    <tr>
      <td>3</td>
      <td>1949-04-01</td>
      <td>129</td>
    </tr>
    <tr>
      <td>4</td>
      <td>1949-05-01</td>
      <td>121</td>
    </tr>
  </tbody>
</table>
</div>



Change the `'Month'` column over to a `datetime` type and make sure it is set as the index of the DataFrame. 


```python
# Change the type of 'Month' to datetime
data['Month'] = pd.to_datetime(data['Month'])

# Set 'Month' as the index
data.set_index('Month', inplace=True)
```


```python
# Check the index
data.info()
```

    <class 'pandas.core.frame.DataFrame'>
    DatetimeIndex: 144 entries, 1949-01-01 to 1960-12-01
    Data columns (total 1 columns):
    #Passengers    144 non-null int64
    dtypes: int64(1)
    memory usage: 2.2 KB


Now that we have successfully created a time series, we can use the `.plot()` method in pandas to visually inspect this time series.


```python
# Plot the time series data 
data.plot()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7fa79a73ec50>




![png](index_files/index_9_1.png)


Wec can see that that there is an overall increasing trend in the data along with some seasonal variations. However, it might not always be possible to make such visual inferences. Let's reconfirm this here using both **rolling statistics** and the **Dickey-Fuller test**.

## Rolling Statistics 

Use the `.rolling()` method to find the rolling mean and rolling std with a window of 12 months. Plot the original curve along with the rolling mean and standard error.


```python
# Determine rolling statistics
roll_mean = data.rolling(window=12, center=False).mean()
roll_std = data.rolling(window=12, center=False).std()
```


```python
# Plot rolling statistics
fig = plt.figure(figsize=(12,6))
plt.plot(data, color='blue',label='Original')
plt.plot(roll_mean, color='red', label='Rolling Mean')
plt.plot(roll_std, color='black', label = 'Rolling Std')
plt.legend(loc='best')
plt.title('Rolling Mean & Standard Deviation')
plt.show();
```


![png](index_files/index_13_0.png)


Though the variation in standard deviation is small, the mean is increasing with time and thus, this is not a stationary series. 

## Dickey-Fuller Test 

Use the Dickey-Fuller test to verify your visual result.


```python
from statsmodels.tsa.stattools import adfuller
adf_test = adfuller(data['#Passengers'])

adf_output = pd.Series(adf_test[0:4], index=['Test Statistic', 'p-value', '#Lags Used', 'Number of Observations Used'])
for key,value in adf_test[4].items():
    adf_output['Critical Value (%s)'%key] = value
print(adf_output)
```

    Test Statistic                   0.815369
    p-value                          0.991880
    #Lags Used                      13.000000
    Number of Observations Used    130.000000
    Critical Value (1%)             -3.481682
    Critical Value (5%)             -2.884042
    Critical Value (10%)            -2.578770
    dtype: float64


## Summary

In this lab, you checked for the stationarity of a time series in Python. Next, we'll further explore stationarity and how to make sure to make time series stationary!
