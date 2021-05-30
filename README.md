# Cryptocurrencies-Testing-for-heteroskedasticity-multicollinearity-serial-autocorrelation-

Task: Run an OLS multiple regression model using any combination of ten assets and ten macroeconomic variables. 

The daily spot prices of the following ten (crypto) currencies were examined: Bitcoin (BTC), Bitcoin Cash (BCH), Litecoin (LTC), Ethereum (ETH), Euro (EUR), Swiss franc (CHF), Pound sterling (GBP ), Russian ruble (RUB), Japanese yen (JPY), Swedish krona (SEK). The daily sample includes data from November 1, 2018 to January 1, 2021. Various data sets were drawn from Thomson Reuters Eikon for the investigation.

The main objective of this task was to compare each of the ten currencies (5 crypto and 5 fiat) with an OLS multiple regression model using any combination of ten macroeconomic variables. To identify relationships, the listed commodity prices and indices represented the ten macroeconomic variables. Due to the fact, that cryptocurrency records have a short historical horizon compared to the other assets examined (fiat currency, commodity, and indices), this paper uses daily historical market data. Data on cryptocurrency is only from 2018 to 2020 available on Thomson Reuters Eikon. All regressions were performed in the Python programming language.

(1) commodities: oil (WTI brent), gold, silver, palladium, platinum 
(2) indices: MSCI, NASDAQ, SMI, DAX, FTSE 100


# Main results and conclusion:

In the period under review, there was the highest linear correlation between Swiss franc and euro. And the second highest linear correlation between the price of gold and silver. The analysis revealed that Gold, MSCI and NASDAQ are significant in almost every regression. In contrast, silver, oil, DAX and SMI show the least significance. Bitcoin Cash could only be explained by 5% of the variables. It is noticeable that all crypto currencies are more difficult to explain than fiat currencies by the specified variables. 

A check for multicollinearity has shown that there is no multicollinearity. This was also confirmed by the VIF-test. All results were below 4, which shows that there is no multicollinearity given. Thus, the results were not biased. The examination with the Breusch-Pagan test showed, that heteroscedasticity is present. The results of the Breusch-Pagan test show p-values below 0.05. The results should therefore be treated with caution. Because the results were not representative due to het-eroscedasticity, the regressions with heteroscedasticity were filtered out of the table and sorted according to the adjusted R2. Even after this process, the null hypothesis could not be rejected and therefore no conclusion could be drawn. There is still uncertainty. The presence of autocorrelation was also checked. All results of the performed Durbin-Watson tests gave values below 2.5, which is in the range of 1.5 and 2.5. Consequently, autocorrelation is not considered a problematic aspect in the top 10 regression models.

In conclusion, it has been shown that multicollinearity, autocorrelation, heteroscedasticity do not perfectly explain the price movements and developments of the ten (crypto) currencies. 


# Codes used for start the regression analysis:

df = df.copy()
#unrelevant regression - just to get different values of OLS that can be included in final output
y = df['BTC_log']
x = df[['Gold', 'Silver','Palladium','Platinum','OIL','MSCI', 'FTSE_100','NASDAQ','DAX', 'SMI']]
x = sm.add_constant(x)
model = sm.OLS(y,x, missing = 'drop') 
results = model.fit()

#list all different values that the results object of an OLS can return to select for loop
dir(results) 

dflog=dflog.dropna()
target_variables_log = ['BTC_log', 'BCH_log', 'LTC_log', 'ETH_log','EUR_log', 'CHF_log', 'GBP_log', 'RUB_log', 'YEN_log', 'SEK_log']

#x = df[['Gold', 'Silver','Palladium','Platinum','OIL','MSCI', 'FTSE_100','NASDAQ','DAX', 'SMI']]
##x = sm.add_constant(x)

x = df[['Gold_log', 'Silver_log', 'Palladium_log', 'Platinum_log', 'OIL_log', 'MSCI_log', 'FTSE_100_log', 'NASDAQ_log', 'DAX_log', 'SMI_log']]
x = sm.add_constant(x)

explanatory_variables = x.columns

results_of_interest_log = []
for i in explanatory_variables: results_of_interest_log.append('params.'+i)
for i in explanatory_variables: results_of_interest_log.append('bse.'+i)
for i in explanatory_variables: results_of_interest_log.append('pvalues.'+i)    
    
results_of_interest_log.extend(('rsquared','rsquared_adj','aic','autocorrelation', 'hetero-scedasticity_lm', 'heteroscedasticity_lm_pvalue'))#, 'multicollinearity'))
results_of_interest_log

# list/array with all regression combinations
regression_combinations_log = []

explanatory_variables_log = ['Gold_log', 'Sil-ver_log','Palladium_log','Platinum_log','OIL_log','MSCI_log', 'FTSE_100_log','NASDAQ_log','DAX_log', 'SMI_log']
for n in range(0, len(explanatory_variables_log)+1):
    regres-sion_combinations_log.extend(list(itertools.combinations(explanatory_variables_log, n)))

#creation of all rows
rows = []
for asset in target_variables_log: 
    for i in range(1,len(regression_combinations_log)+1): rows.append(asset+str(i))   
len(rows)

# loop regression combination with rows

df_loop_log = pd.DataFrame(index=rows, columns=['y', 'x'])

for i in range(0,len(rows)):
    df_loop_log['y'][i] = tar-get_variables_log[math.floor(i/len(regression_combinations_log))]
    df_loop_log['x'][i] = list(regression_combinations_log[int(math.remainder(i,len(regression_combinations_log)))])

df_loop_log

#create empfty dataframe to fill during the loop
output_table_log = pd.DataFrame(index=rows, columns=results_of_interest_log)
#loop with regression of each asset over all 10 variables
for i in range(0,len(rows)):
        x = dflog[df_loop_log['x'][i]]
        x = sm.add_constant(x)
        y = dflog[df_loop_log['y'][i]]
        model = sm.OLS(y, x, missing = 'drop') 
        results = model.fit()

        for result in results_of_interest_log[:-3]:
            try: output_table_log[result][i] = eval("results."+result)
            except: pass

        output_table_log["autocorrelation"][i] = durbin_watson(results.resid)

        het = statsmodels.stats.diagnostic.het_breuschpagan(results.resid, exog_het = x)
        output_table_log["heteroscedasticity_lm"][i] = het[0]
        output_table_log["heteroscedasticity_lm_pvalue"][i] = het[1]

        #output_table["multicollinearity"][asset] = add function that returns multicollinear-ity

# Safe this Results -> Database SQL as Regression Results

conn = sqlite3.connect(r'C:\Users\HPspectre\Desktop\AQM_Assignment\Database\AQM_Database.db')

# Write the new DataFrame to a new SQLite table
output_table_log.to_sql("RegressionResults", conn, if_exists="replace")
conn.close()


Analysis of the Regression:

df=output_table_log.copy()
df.index.name='ID'
#df.reset_index().set_index('', drop=False)
df = df.reset_index()
df.head()

# separate all Data into new Dataframes
dfBTC = df.loc[df['ID'].str.startswith('BTC')].copy()
dfBCH = df.loc[df['ID'].str.startswith('BCH')].copy()
dfLTC = df.loc[df['ID'].str.startswith('LTC')].copy()
dfETH = df.loc[df['ID'].str.startswith('ETH')].copy()
dfEUR= df.loc[df['ID'].str.startswith('EUR')].copy()
dfCHF = df.loc[df['ID'].str.startswith('CHF')].copy()
dfGBP = df.loc[df['ID'].str.startswith('GBP')].copy()
dfRUB = df.loc[df['ID'].str.startswith('RUB')].copy()
dfYEN = df.loc[df['ID'].str.startswith('YEN')].copy()
dfSEK= df.loc[df['ID'].str.startswith('SEK')].copy()

dfBTC.set_index('ID', inplace=True)
dfBTC=dfBTC[['rsquared_adj','aic','autocorrelation','heteroscedasticity_lm_pvalue']]
dfBTC.sort_values(by=['rsquared_adj'], ascending=False, inplace = True)
dfBTC.head()

dfBCH.set_index('ID', inplace=True)
dfBCH=dfBCH[['rsquared_adj','aic','autocorrelation','heteroscedasticity_lm_pvalue']]
dfBCH.sort_values(by=['rsquared_adj'], ascending=False, inplace = True)
dfBCH.head()

dfLTC.set_index('ID', inplace=True)
dfLTC=dfLTC[['rsquared_adj','aic','autocorrelation','heteroscedasticity_lm_pvalue']]
dfLTC.sort_values(by=['rsquared_adj'], ascending=False, inplace = True)
dfLTC.head()

dfETH.set_index('ID', inplace=True)
cols = list(dfETH.columns.values)
dfETH=dfETH[['rsquared_adj','aic','autocorrelation','heteroscedasticity_lm_pvalue']]
dfETH.sort_values(by=['rsquared_adj'], ascending=False, inplace = True)
dfETH.head()

dfEUR.set_index('ID', inplace=True)
dfEUR=dfEUR[['rsquared_adj','aic','autocorrelation','heteroscedasticity_lm_pvalue']]
dfEUR.sort_values(by=['rsquared_adj'], ascending=False, inplace = True)
dfEUR.head()

dfCHF.set_index('ID', inplace=True)
cols = list(dfCHF.columns.values)
dfCHF=dfCHF[['rsquared_adj','aic','autocorrelation','heteroscedasticity_lm_pvalue']]
dfCHF.sort_values(by=['rsquared_adj'], ascending=False, inplace = True)
dfCHF.head()

dfGBP.set_index('ID', inplace=True)
cols = list(dfGBP.columns.values)
dfGBP=dfGBP[['rsquared_adj','aic','autocorrelation','heteroscedasticity_lm_pvalue']]
dfGBP.sort_values(by=['rsquared_adj'], ascending=False, inplace = True)
dfGBP.head()

dfRUB.set_index('ID', inplace=True)
cols = list(dfRUB.columns.values)
dfRUB=dfRUB[['rsquared_adj','aic','autocorrelation','heteroscedasticity_lm_pvalue']]
dfRUB.sort_values(by=['rsquared_adj'], ascending=False, inplace = True)
dfRUB.head()

dfYEN.set_index('ID', inplace=True)
cols = list(dfYEN.columns.values)
dfYEN=dfYEN[['rsquared_adj','aic','autocorrelation','heteroscedasticity_lm_pvalue']]
dfYEN.sort_values(by=['rsquared_adj'], ascending=False, inplace = True)
dfYEN.head()

dfSEK.set_index('ID', inplace=True)
cols = list(dfSEK.columns.values)
dfSEK=dfSEK[['rsquared_adj','aic','autocorrelation','heteroscedasticity_lm_pvalue']]
dfSEK.sort_values(by=['rsquared_adj'], ascending=False, inplace = True)
dfSEK.head()

DF = pd.DataFrame(columns=['rsquared_adj', 'aic','autocorrelation','heteroscedasticity_lm_pvalue'])
DF

DF=DF.append(dfBTC.head(1))
DF=DF.append(dfBCH.head(1))
DF=DF.append(dfLTC.head(1))
DF=DF.append(dfETH.head(1))
DF=DF.append(dfEUR.head(1))
DF=DF.append(dfCHF.head(1))
DF=DF.append(dfGBP.head(1))
DF=DF.append(dfRUB.head(1))
DF=DF.append(dfYEN.head(1))
DF=DF.append(dfSEK.head(1))
DF.sort_values(by=['rsquared_adj'], ascending=False, inplace = True)
DF

# Safe this Results -> Database SQL as HighestadjR^2Results 

conn = sqlite3.connect(r'C:\Users\HPspectre\Desktop\AQM_Assignment\Database\AQM_Database.db')

# Write the new DataFrame to a new SQLite table
DF.to_sql("HighestadjR^2Results", conn, if_exists="replace")
conn.close()
