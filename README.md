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

### get the odds ratio, confidence interval (upper and lower) and p-value using the statsmodels library




### helpful resources & thanks
- https://stackoverflow.com/questions/43261747/a-better-way-to-calculate-odd-ratio-in-pandas
- https://select-statistics.co.uk/calculators/confidence-interval-calculator-odds-ratio/

### resources that may enhance my code (to be looked at later)
- https://fangya18.com/2018/10/20/odds-ratio/
- https://www.statsmodels.org/stable/contingency_tables.html
