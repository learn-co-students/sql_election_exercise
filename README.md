# French Connection SQL WARMUP 

Remember how to connect to an sqlite database?

We need to create a connection and a cursor.  


```python
import sqlite3

#connect to the db 'parlgov-development' in the data folder

conn = None
cursor = None
```


```python
#__SOLUTION__
#connect to the db 'parlgov-development' in the data folder
import sqlite3

conn = sqlite3.connect('data/parlgov-development.sqlite')
cursor = conn.cursor()
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
# This query helps us familiarize ourselves to the table
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


```python
#__SOLUTION__
cursor.execute('''SELECT DISTINCT country_name_short 
                    FROM view_election 
                ''')
cursor.fetchall()
```




    [('AUS',),
     ('AUT',),
     ('BEL',),
     ('BGR',),
     ('CAN',),
     ('CHE',),
     ('CYP',),
     ('CZE',),
     ('DEU',),
     ('DNK',),
     ('ESP',),
     ('EST',),
     ('FIN',),
     ('FRA',),
     ('GBR',),
     ('GRC',),
     ('HRV',),
     ('HUN',),
     ('IRL',),
     ('ISL',),
     ('ISR',),
     ('ITA',),
     ('JPN',),
     ('LTU',),
     ('LUX',),
     ('LVA',),
     ('MLT',),
     ('NLD',),
     ('NOR',),
     ('NZL',),
     ('POL',),
     ('PRT',),
     ('ROU',),
     ('SVK',),
     ('SVN',),
     ('SWE',),
     ('TUR',)]



# Query 2: 

Using the short name for France seen above, count how many elections occured in France during the 1990's.

HINT: [built-in-aggregates](https://www.sqlite.org/lang_aggfunc.html)  
HINT: Use full string of date: Ex: "2021-28-21"


```python
#__SOLUTION__
query = '''SELECT COUNT(*) FROM view_election
            WHERE country_name_short = "FRA"
            AND election_date >= "1990-01-01"
            AND election_date < "2000-01-01"
'''

cursor.execute(query)
cursor.fetchall()
```




    [(48,)]



# Query 3: 

What was the average vote share per party in the UK during the 2000's.
Order by average votes descending.
Hint: Use GROUP BY operator (group by needs an aggregate)


```python
#__SOLUTION__
query = '''SELECT party_name, AVG(vote_share) as avg_vote_share FROM view_election
            WHERE country_name_short = "GBR"
            AND election_date >= "2000-01-01" 
            AND election_date < "2010-01-01" 
            GROUP BY party_name
            ORDER BY avg_vote_share DESC

'''

cursor.execute(query)
cursor.fetchall()
```




    [('Conservatives', 29.625),
     ('Labour Party', 28.55),
     ('Liberals', 17.225),
     ('United Kingdom Independence Party', 9.1),
     ('British National Party', 5.550000000000001),
     ('Green Party', 5.233333333333333),
     ('English Democrats', 1.8),
     ('Scottish National Party – Pàrtaidh Nàiseanta na h-Alba',
      1.7000000000000002),
     ('The Christian Party – Christian Peoples Alliance in England', 1.6),
     ('Socialist Labour Party', 1.1),
     ('NO2EU – Yes to Democracy', 1.01),
     ('Respect – The Unity Coalition', 0.89),
     ('Democratic Unionist Party', 0.7975),
     ('Plaid Cymru', 0.7749999999999999),
     ('Sinn Féin', 0.7375),
     ('Ulster Unionist Party', 0.5925),
     ('Social Democratic and Labour Party', 0.55),
     ('others', None)]



# Query 4

Produce a list of all French presidents in the database.  Order by their start dates. 
HINT: Query the politician_president table.  This table has a country_id associated with the politician.  Join the country table to the politician_president table, and filter by `name_short` which matches the short name of france you used above. 


```python
#__SOLUTION__
query = '''SELECT DISTINCT p.person_id_source
            FROM politician_president p 
            JOIN country c
            ON p.country_id = c.id
            WHERE c.name_short='FRA'
            ORDER BY p.start_date

'''

cursor.execute(query)
cursor.fetchall()
```




    [('Armand Fallières',),
     ('Raymond Poincaré',),
     ('Paul Deschanel',),
     ('Alexandre Millerand',),
     ('Gaston Doumergue',),
     ('Paul Doumer',),
     ('Albert Lebrun',),
     ('Philippe Pétain',),
     ('Vincent Auriol',),
     ('René Coty',),
     ('Charles de Gaulle',),
     ('Georges Pompidou',),
     ("Valéry Giscard d'Estaing",),
     ('François Mitterrand',),
     ('Jacques Chirac',),
     ('Nicolas Sarkozy',),
     ('François Hollande',),
     ('',)]



# DON'T FORGET TO CLOSE THE DB CONNECTION WHEN YOU'RE DONE QUERYING!!!!!


```python
#your code to close the connection here
conn.close()
```


```python

```
