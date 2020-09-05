---
title: '[Pandas] How to import from Sql Server'
date: 2018-10-18 20:08:25
categories: 'Data&AI'
tags: 'Pandas'
---

We need to rely on pyodbc, the sample code is as belows.

```python
import pyodbc
cnxn = pyodbc.connect('DRIVER={ODBC Driver 13 for SQL Server};SERVER=SQLSERVER2017;DATABASE=Adventureworks;Trusted_Connection=yes')
```

Write SQL and execute with pandas.read_sql

```Python
import pandas
query = "SELECT * from a"
df = pd.read_sql(query, sql_conn)
```

