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
![Picture2](https://github.com/dingy21/salesforecasting/assets/134649288/176a7a06-b35d-4685-b951-5a1257cb761d)
![Picture7](https://github.com/dingy21/salesforecasting/assets/134649288/c6268320-7f9c-4bc2-a826-42932b632a77)
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
![Picture5](https://github.com/dingy21/salesforecasting/assets/134649288/b128f025-2c10-4113-8524-8dfb1cd8f91f)
![Picture6](https://github.com/dingy21/salesforecasting/assets/134649288/c325d1ed-9746-4ca5-9e0a-426eb22c871a)
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
![Picture8](https://github.com/dingy21/salesforecasting/assets/134649288/d5829af1-1886-403f-b3d4-52e6e309fbde)
![Picture9](https://github.com/dingy21/salesforecasting/assets/134649288/b6319a05-df40-4bbc-b7cc-4cf53c263bdf)
### Compare to auto.arima
```
fit_blouses_auto <- auto.arima(blouses_ts)
summary(fit_blouses_auto)
checkresiduals(fit_blouses_auto)
accuracy(fit_blouses_auto)

forecast(fit_blouses_auto, h=3)

fit_blouses_auto %>% forecast() %>% autoplot()
```
