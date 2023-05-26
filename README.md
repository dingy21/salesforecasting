# Demand Forecasting Project
Forecasting sales demand enables businesses to effectively plan production, allocate resources, and meet customer needs, ultimately maximizing profitability and minimizing waste. In this project, exponential smoothing was used to optimize time series forecasting models.
## Load Library
```
library(tidyverse)
library(tidymodels)
library(janitor)
library(skimr)
library(kableExtra)
library(GGally)
library(vip)        
library(fastshap)   
library(MASS)
library(ISLR)
library(tree)
library(ggplot2)
library(dplyr)
library(lubridate)
library(imputeTS)
library(imputeTS)
library(forecast)
library(urca)
library(pracma)
library(fpp2)
library(forecast)
```
## Import Data
```
sales <- read_csv("RetailSales2.csv") %>% clean_names()
head(sales)
```
## Create Time Series Objects & Exploratory Graphs
```
# -- time series for laces
laces <- subset(sales, select = c(laces))
laces_ts <- ts(laces, start = c(1994,1), frequency = 12)
plot(laces_ts)
ggAcf(laces_ts)
ggPacf(laces_ts)

# -- time series for blouses
blouses <- subset(sales, select = c(blouses))
blouses_ts <- ts(blouses, start = c(1994,1), frequency = 12)
plot(blouses_ts)
ggAcf(blouses_ts)
ggPacf(blouses_ts)
```
![Picture3](https://github.com/dingy21/salesforecasting/assets/134649288/6320ebda-848d-4d62-85b4-48394413b0e7)
![Picture4](https://github.com/dingy21/salesforecasting/assets/134649288/d437a814-277e-432d-9f3b-4cb53dff7d8d)
![Picture2](https://github.com/dingy21/salesforecasting/assets/134649288/9f79a5b9-5964-4ba6-bd32-46ebf0a27d8a)
## Forecasting Demand for Laces
### Fit SES Model
```
laces_ses <- ses(laces_ts, h=3)
summary(laces_ses)
checkresiduals(laces_ses)

forecast(laces_ses)
laces_ses %>% forecast() %>% autoplot()
```
### Use Holt Winters to Incorporate Seasonality
```
laces_hw <- hw(laces_ts, seasonal = "additive", h=3)
summary(laces_hw)
checkresiduals(laces_hw)

laces_hw %>% forecast() %>% autoplot()
```
### Fit Seasonal Exponential Smoothing Model
```
laces_ets <- ets(laces_ts, model = "ZNZ")
summary(laces_ets)
checkresiduals(laces_ets)

forecast(laces_ets, h=3)

laces_ets %>% forecast() %>% autoplot()
```
### Compare to auto.arima
```
fit_laces_auto <- auto.arima(laces_ts)
summary(fit_laces_auto)
checkresiduals(fit_laces_auto)
accuracy(fit_laces_auto)

forecast(fit_laces_auto, h=3)
fit_laces_auto %>% forecast() %>% autoplot()
```
## Forecasting Demand for Blouses
### Fit SES Model
```
blouses_ses <- ses(blouses_ts, h=3)
summary(blouses_ses)
checkresiduals(blouses_ses)

forecast(blouses_ses)
blouses_ses %>% forecast() %>% autoplot()
```
### Use Holt Winters to Incorporate Trend and Seasonality
```
# -- additive
blouses_hwA <- hw(blouses_ts, seasonal = "additive", h=3)
summary(blouses_hwA)
checkresiduals(blouses_hwA)

blouses_hwA %>% forecast() %>% autoplot()

# -- multiplicative
blouses_hwM <- hw(blouses_ts, seasonal = "multiplicative", h=3)
summary(blouses_hwM)
checkresiduals(blouses_hwM)

blouses_hwM %>% forecast() %>% autoplot()
```
### Fit Seasonal Exponential Smoothing Model
```
blouses_ets <- ets(blouses_ts, model = "ZZZ")
summary(blouses_ets)
checkresiduals(blouses_ets)

forecast(blouses_ets, h=3)

blouses_ets %>% forecast() %>% autoplot()
```
### Compare to auto.arima
```
fit_blouses_auto <- auto.arima(blouses_ts)
summary(fit_blouses_auto)
checkresiduals(fit_blouses_auto)
accuracy(fit_blouses_auto)

forecast(fit_blouses_auto, h=3)

fit_blouses_auto %>% forecast() %>% autoplot()
```
