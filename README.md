Remember how to connect to an sqlite database?

We need to create a connection and a cursor.  


```python
import sqlite3

#connect to the db 'parlgov-development' in the data folder

conn = None
cursor = None
```


```python
# Inspect the tables in the database
cursor.execute('''SELECT name
               FROM sqlite_master WHERE type="table"''')
cursor.fetchall()
```




    [('info_data_source',),
     ('external_party_castles_mair',),
     ('external_party_chess',),
     ('external_party_huber_inglehart',),
     ('info_table',),
     ('external_party_euprofiler',),
     ('party_family',),
     ('info_id',),
     ('sqlite_stat1',),
     ('external_party_benoit_laver',),
     ('external_country_iso',),
     ('viewcalc_party_position',),
     ('viewcalc_election_parameter',),
     ('viewcalc_parliament_composition',),
     ('viewcalc_country_year_share',),
     ('info_variable',),
     ('party_name_change',),
     ('election',),
     ('election_result',),
     ('cabinet_party',),
     ('country',),
     ('party',),
     ('party_change',),
     ('external_party_cmp',),
     ('external_party_ees',),
     ('politician_president',),
     ('external_party_ray',),
     ('external_commissioner_doering',),
     ('cabinet',)]



### We will make some queries of the view_election table


```python
# There are 8623 elections described in the dataset

cursor.execute('''SELECT * 
                      FROM view_election 
                  LIMIT 1
                ''')
cursor.fetchall()
```




    [('AUS',
      'Australia',
      'parliament',
      '1901-03-30',
      44.4,
      32,
      75,
      'PP',
      'Protectionist Party',
      'Protectionist Party',
      7.4,
      33,
      731,
      None,
      None,
      1898)]



After running the cell above, our cursor has a description attribute. We can use this attribute to inspect the column names in the view_election table.


```python
cursor.description
```




    (('country_name_short', None, None, None, None, None, None),
     ('country_name', None, None, None, None, None, None),
     ('election_type', None, None, None, None, None, None),
     ('election_date', None, None, None, None, None, None),
     ('vote_share', None, None, None, None, None, None),
     ('seats', None, None, None, None, None, None),
     ('seats_total', None, None, None, None, None, None),
     ('party_name_short', None, None, None, None, None, None),
     ('party_name', None, None, None, None, None, None),
     ('party_name_english', None, None, None, None, None, None),
     ('left_right', None, None, None, None, None, None),
     ('country_id', None, None, None, None, None, None),
     ('election_id', None, None, None, None, None, None),
     ('previous_parliament_election_id', None, None, None, None, None, None),
     ('previous_cabinet_id', None, None, None, None, None, None),
     ('party_id', None, None, None, None, None, None))



# Query 1: 
Run a query that shows us all the unique country names (the short version).

# Query 2: 

Using the short name for France seen above, count how many elections occured in France during the 1990's.

HINT: [built-in-aggregates](https://www.sqlite.org/lang_aggfunc.html)  
HINT: Use full string of date: Ex: "2021-28-21"

# Query 3: 

What was the average vote share per party in the UK during the 2000's.
Order by average votes descending.
Hint: Use GROUP BY operator (group by needs an aggregate)

# DON'T FORGET TO CLOSE THE DB CONNECTION WHEN YOU'RE DONE QUERYING!!!!!


```python
#your code to close the connection here
conn.close()
```
