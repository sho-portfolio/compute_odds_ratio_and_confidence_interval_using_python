## code to compute odds ratio, risk ratio and confidence interval at different levels from time-series data in mysql using python

### get time-series data from mysql

```python
from sqlalchemy import create_engine
import pymysql
import pandas as pd

db_connection_str = 'mysql+pymysql://root:london12@localhost/healthdb'
db_connection = create_engine(db_connection_str)

df_final = pd.read_sql(""" SELECT fast, weight_loss FROM tblTimeSeriesDataEnhanced ORDER BY date; """, con=db_connection)

print(df_final)
```
