# Cryptocurrencies-Testing-for-heteroskedasticity-multicollinearity-serial-autocorrelation-

Task: Run an OLS multiple regression model using any combination of ten assets and ten macro macroeconomic variables. 

The daily spot prices of the following ten (crypto) currencies were examined: Bitcoin (BTC), Bitcoin Cash (BCH), Litecoin (LTC), Ethereum (ETH), Euro (EUR), Swiss franc (CHF), Pound sterling (GBP ), Russian ruble (RUB), Japanese yen (JPY), Swedish krona (SEK). The daily sample includes data from November 1, 2018 to January 1, 2021. Various data sets were drawn from Thomson Reuters Eikon for the investigation.

The main objective of this task was to compare each of the ten currencies (5 crypto and 5 fiat) with an OLS multiple regression model using any combination of ten macroeconomic variables. To identify relationships, the listed commodity prices and indices represent the ten macroeconomic variables. Due to the fact, that cryptocurrency records have a short histori-cal horizon compared to the other assets examined (fiat currency, commodity, and indi-ces), this paper uses daily historical market data. Data on cryptocurrency is only from 2018 to 2020 available on Thomson Reuters Eikon. All regressions were performed in the Python programming language.

(1) commodities: oil (WTI brent), gold, silver, palladium , platinum 
(2) indices: MSCI, NASDAQ, SMI, DAX, FTSE 100


# Main results and conclusion:

In the period under review, there was the highest linear correlation between Swiss franc and euro. And the second highest linear correlation between the price of gold and silver. The analysis revealed that Gold, MSCI and NASDAQ are significant in almost every regression. In contrast, silver, oil, DAX and SMI show the least significance. Bitcoin Cash could only be explained by 5% of the variables. It is noticeable that all crypto cur-rencies are more difficult to explain than fiat currencies by the specified variables. 

A check for multicollinearity has shown that there is no multicollinearity. This was also confirmed by the VIF-test. All results were below 4, which shows that there is no multi-collinearity given. Thus, the results were not biased. 
The examination with the Breusch-Pagan test showed, that heteroscedasticity is present. The results of the Breusch-Pagan test show p-values below 0.05. The results should therefore be treated with caution. Because the results were not representative due to het-eroscedasticity, the regressions with heteroscedasticity were filtered out of the table and sorted according to the adjusted R2. Even after this process, the null hypothesis could not be rejected and therefore no conclusion could be drawn. There is still uncertainty. 

The presence of autocorrelation was also checked. All results of the performed Durbin-Watson tests gave values below 2.5, which is in the range of 1.5 and 2.5. Consequently, autocorrelation is not considered a problematic aspect in the top 10 regression models.

In conclusion, it has been shown that multicollinearity, autocorrelation, heteroscedasticity do not perfectly explain the price movements and developments of the ten currencies. 

