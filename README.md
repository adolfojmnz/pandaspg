## Description

This library contains several functions that allow you to migrate data from a CSV file or Pandas Dataframe into a PostgreSQL database using the libraries Psycopg2 and Pandas. Specifically, it includes functions for:

- Loading data from a CSV file to a Pandas dataframe
- Mapping Pandas Dataframe columns to their datatypes
- Mapping Pandas Dataframe columns to suitable PostgreSQL datatypes
- Connect to a PostgreSQL database
- Creating new tables on a PostgreSQL database
- Inserting data from a Pandas DataFrame into a table in a PostgreSQL database
<br> <br>


## Installation

Install The Required Dependencies

```console
pip install pandas psycopg2
```

Install The `pandaspg` Package

```console
pip install pandaspg
```

<aside> 💡 Make sure you have a PostgreSQL database up and running! </aside>
<br>


## Usage

I will walk you through a step-by-step example of how to migrate data from a `CSV` file into a `PostgreSQL` database.
For demonstration purposes, download the [exoplanets](./exoplanets_07-04-2023.csv) file located in this repo.

<br>


### Download The CSV file

```console
wget https://raw.githubusercontent.com/adolfojmnz/pandaspg/main/exoplanets_07-04-2023.csv
```

<aside> 💡 This file contains a total of 34,112 rows and 92 columns with a total size of 28.6 MB </aside>
<br>

### Import The Library

```python
import pandaspg
```

### Create a Pandas `dataframe` with the `CSV` file data

```python
dataframe = pandaspg.csv_to_dataframe('exoplanets_04-07-2023.csv')
```

### Generate a dictionary mapping the dataframe columns to their datatype

```python
column_datatypes_dict = pandaspg.get_dataframe_column_dtypes_dict(dataframe)
```

### Generate a dictionary mapping the dataframe columns to a suitable PostgreSQL datatype

```python
pg_column_datatypes_dict = pandaspg.map_pandas_to_postgresql_datatypes(
    column_datatypes_dict
)
```

### Connect to an existing and running PostgreSQL database

```python
connection = pandaspg.connect_to_postgresql(
    database='analysis',
    user='postgres',
    password='postgres',
    host='localhost',
    port=5432,
)
```

<aside> 💡 Replace the above parameters as you see fit. </aside>


### Create a PostgreSQL table

```python
pandaspg.create_postgresql_table(
    connection, 'exoplanets_csv', pg_column_datatypes_dict
)
```

<aside> 💡 The parameter `exoplanets_csv` is the name of the database to be created. </aside>

### Insert the data from the dataframe into the recently created table

```python
pandaspg.insert_dataframe_into_postgresql(
    connection, 'exoplanets_csv', dataframe
)
```

### Close the connection with the database

```python
connection.close()
```
<br>

## The Full Example

```python
import pandaspg

dataframe = pandaspg.csv_to_dataframe('exoplanets_04-07-2023.csv')

column_datatypes_dict = pandaspg.get_dataframe_column_dtypes_dict(dataframe)

pg_column_datatypes_dict = pandaspg.map_pandas_to_postgresql_datatypes(
    column_datatypes_dict
)

connection = pandaspg.connect_to_postgresql(
    database='analysis',
    user='postgres',
    password='postgres',
    host='localhost',
    port=5432,
)

pandaspg.create_postgresql_table(
    connection, 'exoplanets_csv', pg_column_datatypes_dict
)

pandaspg.insert_dataframe_into_postgresql(
    connection, 'exoplanets_csv', dataframe
)

connection.close()
```
<br>

## Inspect The Results

### Enter the PostgreSQL prompt

```cosnole
psql -U postgres -d analysis
```

### List the tables in the `analysis` database

```sql
\dt
```

### Retrieve the data from the `exoplanets_csv` table

```sql
SELECT * FROM exoplanets_csv;
```