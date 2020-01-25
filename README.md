## code to compute odds ratio, risk ratio and confidence interval at different levels from time-series data in mysql using python

### get time-series data from mysql
```python
from sqlalchemy import create_engine
import pandas as pd

db_connection_str = 'mysql+pymysql://root:london12@localhost/healthdb'
db_connection = create_engine(db_connection_str)

df_final = pd.read_sql(""" SELECT fast, weight_loss FROM tblTimeSeriesDataEnhanced ORDER BY date; """, con=db_connection)

print(df_final)
```

### create a contingency-table (aka 2x2, cross-tab)
```python
import scipy.stats as stats

# contingency table 2x2
ct = pd.crosstab(df_final['fast'], df_final['weight_loss'])
print(ct)

# print(ct[0][0], ct[1][0], ct[0][1], ct[1][1])

oddsratio, pvalue = stats.fisher_exact(ct)
print("odds ratio: ", oddsratio, "p-value:", pvalue)
```

### get the odds ratio, risk ratio, confidence interval @ 95% (upper and lower) and p-value using the statsmodels library
```python
import statsmodels.api as sm
t2x2 = sm.stats.Table2x2(ct)
print (t2x2)
print(t2x2.summary())
# LCB and UCB are the lower and upper confidence index values at 95%
```

### get the odds ratio, confidence interval @ 90%, 95%, 99% (upper and lower) manually
```python
import math

#a=992, b=2260, c=165, d=1017
#a=85, b=22, c=417, d=602
#a=602, b=417, c=22, d=85

#obs = np.array([[602, 417], [22, 85]])
#print(obs[0,0], obs[0,1], obs[1,0], obs[1,1])

a = ct[0][0]
b = ct[1][0]
c = ct[0][1]
d = ct[1][1]
print(ct)


odds_ratio = ((a/c)/(b/d))
print ("odds_ratio:", odds_ratio)

log_odds_ratio = math.log(odds_ratio)
#print(log_odds_ratio)

standard_error_log_odds_ratio = math.sqrt(1/a+1/b+1/c+1/d)
# print(standard_error_log_odds_ratio)

# https://www.statisticshowto.datasciencecentral.com/probability-and-statistics/find-critical-values/
critical_value_90_aka_zscore = 1.645
critical_value_95_aka_zscore = 1.96
critical_value_99_aka_zscore = 2.575

ci_upper_90 = math.exp(math.log(odds_ratio) + critical_value_90_aka_zscore * standard_error_log_odds_ratio)
ci_lower_90 = math.exp(math.log(odds_ratio) - critical_value_90_aka_zscore * standard_error_log_odds_ratio)
print("ci_lower_90:", ci_lower_90)
print("ci_upper_90:", ci_upper_90)

ci_upper_95 = math.exp(math.log(odds_ratio) + critical_value_95_aka_zscore * standard_error_log_odds_ratio)
ci_lower_95 = math.exp(math.log(odds_ratio) - critical_value_95_aka_zscore * standard_error_log_odds_ratio)
print("ci_lower_95:", ci_lower_95)
print("ci_upper_95:", ci_upper_95)

ci_upper_99 = math.exp(math.log(odds_ratio) + critical_value_99_aka_zscore * standard_error_log_odds_ratio)
ci_lower_99 = math.exp(math.log(odds_ratio) - critical_value_99_aka_zscore * standard_error_log_odds_ratio)
print("ci_lower_99:", ci_lower_99)
print("ci_upper_99:", ci_upper_99)
```



### helpful resources & thanks
- https://stackoverflow.com/questions/43261747/a-better-way-to-calculate-odd-ratio-in-pandas
- https://select-statistics.co.uk/calculators/confidence-interval-calculator-odds-ratio/
- https://www.statisticshowto.datasciencecentral.com/probability-and-statistics/find-critical-values/
- http://sphweb.bumc.bu.edu/otlt/MPH-Modules/PH717-QuantCore/PH717_ComparingFrequencies/PH717_ComparingFrequencies8.html

### resources that may enhance my code (to be looked at later)
- https://fangya18.com/2018/10/20/odds-ratio/
- https://www.statsmodels.org/stable/contingency_tables.html
