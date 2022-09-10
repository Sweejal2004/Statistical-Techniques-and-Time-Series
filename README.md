# Statistical-Techniques-and-Time-Series
S&amp;P500 index price daily data for the period of 2009/01/01 to 2020/12/31

## The below finding includes the statistical time series on S&P500 Price daily data.

## I. Downloading S&P500 Daily Time series Data

getSymbols("^GSPC",from = "2009-01-01", to = "2020-12-31")

![Time series](https://user-images.githubusercontent.com/109311890/189486961-c184f3ec-7840-45fe-99f8-801ab1cb630c.JPG)

                  Figure 1 Daily Time series of S&P500 for period '2009-01-01' to '2020-12-31'
The above plot shows the time series for S&P 500 for period ‘'2009-01-01' to '2020-12-31'

## II. Log-returns of Time Series

The below code shows the Log-return of Adjusted close price.

![log-returns](https://user-images.githubusercontent.com/109311890/189487042-a9aa8d9c-6fc0-4673-a1fc-71b854ea758c.JPG)

## III. Examine the ACF and PACF functions
acf(log_ret) pacf(log_ret)

![acf_Lag](https://user-images.githubusercontent.com/109311890/189487073-a29257ec-cc0d-4727-9aa4-729701fe90a0.png)

![PACF_Lag](https://user-images.githubusercontent.com/109311890/189487063-3b06023a-8e5c-46b3-a6d2-82a0389be6d9.png)

                    Figure 2 ACF and PACF Plot
                    
There are many significant spikes in both ACF and PACF. In the beginning of the PCF there are 2
Significant spikes indicates the autocorrelation between lags, where the ACF indicates the lagged 
residual errors which is 2 in beginning and the spikes decays slowly to zero after some lags. The 
vertical lines between the confidence interval defined the white noise signal. So, we can consider our 
lag order as AR=2( p=2), I=1(once difference in log-return), MA=2(q=2)based on the above Plots.


## IV. Ljung-Box test and the test-hypothesis

### Box.test(sp500, type = "Ljung-Box")
### Output:data: sp500
### X-squared = 3011.5, df = 1, p-value < 2.2e-16

Ljung-Box test is a statistical test to check if a time series contains any autocorrelation.
The p-value is less than 0.05 threshold value thus we can reject the null hypothesis and the 
hypothesis is alternative hypothesis, indicating the time series contains significant
autocorrelations, whereas a p-value exceeding 0.05 provides no such evidence and if the pvalue is greater than 0.05 implies cannot reject null hypothesis.

The formula for the null hypothesis is: H0: p = p0
The formula for the alternative hypothesis is: Ha = p >p0, < p0≠ p0

## V. Checking the data for stationarity using ADF test

Code: adf.test(sp500)
Augmented Dickey-Fuller Test
data: sp500
Dickey-Fuller = -3.8165, Lag order = 14, p-value = 0.01815
alternative hypothesis: stationary
Equation: Δyt =yt−yt−1=α+βt+γyt−1+et

From above graph and Augmented Dickey-Fuller test we can notice that there is stationarity 
presents in time series and time series is contains volatility in some period. There is no unitroot in the time series, meaning that the time series is stationary i.e., alternative hypothesis
and p-value is less than 0.05 implies we can reject the null hypothesis. The null hypothesis 
implies data are non-stationary.

## VI. A normality on the return series

Code: qqnorm(log_ret)
qqline(log_ret)

![qq_plot](https://user-images.githubusercontent.com/109311890/189487156-097a3fcf-d09a-4669-8d0b-dc08da96cb0e.JPG)

                  Figure 3 Q-Q Plot for normality test of time series
                  
Code: sf.test(log_ret)
Output: Shapiro-Francia normality test
data: log_ret
W = 4.8722e-05, p-value < 2.2e-16

Equation for Shapiro normality test: 

![equation](https://user-images.githubusercontent.com/109311890/189487228-0f337514-f933-42f5-9982-6e05788d2e84.JPG)

From above QQ-plot It can be notice that the dataset points are far from the normal line in 
both ends of the curve, which means that this time series is not normal. The null hypothesis 
of the test is that the distribution is normal Therefore, if p-value of the test is >0.05, we do 
not reject the null hypothesis. But here the shapiro wilk test p-value is < 0.05 that we can 
conclude that we can reject the null hypothesis, which means that our distribution is not 
normal.

## VII. Fit an ARIMA model and determine the correct lag order:

![ARIMA Model](https://user-images.githubusercontent.com/109311890/189487284-60fc5454-6d1d-44c1-94f6-fc9d07242559.JPG)

Here, the auto.arima fit model has set the AR=4 and MA=4 as best model.but while checking 
residuals for the auto fitted model the p-value is 0.00681 which is less than threshold value for 
checking any pattern in the series. However, the model with p,d,q=(6,1,2) this model gives 
good P-value and it denotes that there is no hidden information in the series and can be a good 
fit model to forecast the future value though the AIC is less than an auto fitted model with the
difference of the 1 point.

## VIII. Coefficients for the chosen ARIMA model and equation 

![coefficient](https://user-images.githubusercontent.com/109311890/189487324-6e44db77-770b-4487-985a-36f3e25b8680.JPG)

Equation for chosen model parameters:
![coefficient1](https://user-images.githubusercontent.com/109311890/189487352-b34a4d5b-3151-426e-b872-27fe96cc744a.JPG)

## IX. The residuals from an ARIMA fit 

![residual_withcode](https://user-images.githubusercontent.com/109311890/189487387-69d4b423-fb03-44e0-9273-1dc8bd0dc227.JPG)

                          Figure 4 Residuals for ARIMA Model
                          
From the above residual diagnostics, it can be notice that the residual errors seem to fluctuate 
around a mean of zero and have a uniform variance so we can confirm that residual has zero mean.
The density plot suggests normal distribution with mean zero. The Correlogram, aka, ACF plot 
shows there are some residual errors are outside the confidence level and are still dependent that 
mean there are autocorrelation presents in the model, but The p-value in the Ljung–Box test is 
large, indicating that the residuals have no hidden pattern denotes all the information has been 
extracted by the model and only noise is left behind. So we can conclude that the model is not 
good fit but compare to auto.Arima() model it can be a better option.

## Forecasting of future Adjusted Log price:

Code: fc%>%forecast(h=10)%>%autoplot(include=100)

![forecast](https://user-images.githubusercontent.com/109311890/189487426-47f3ab8e-0034-4f93-8813-f72295f265f2.JPG)

                Figure 5 Forecasting of future Adjusted Log Price
