# Data Modeling with Postgres
>
This project focuses on data modeling<sup>[1](#myfootnote1)</sup> techniques with Postgres<sup>[2](#myfootnote1)</sup> to build an ETL pipeline using Python.

Two datasets, which contain the songs and the user activities in JSON format, were analyzed. A star schema optimized for queries on song play analysis and an ETL pipeline are presented in the jupyter notebook, together with a test notebook to confirm the validity of the database.

<a name="myfootnote1"><sup>1</sup></a> Data modeling is the process of creating a model for the data to store in a database.
Think of data modeling as the process of designing data and making it available to machine learning engineers, data scientists, business analytics, etc., so they can make use of it easily.

<a name="myfootnote1"><sup>2</sup></a> Postgres, or PostgreSQL, is a commonly used open-source relational database management system (RDBMS). It can be deployed [locally](https://www.codementor.io/@engineerapart/getting-started-with-postgresql-on-mac-osx-are8jcopb) or [in the cloud](https://aws.amazon.com/rds/postgresql/).

## Table of contents

* [Introduction](#introduction)
* [Project Description](#project-description)
* [Dataset](#dataset)
* [Database and the Star Schema Design](#database-and-the-star-schema-design)
* [Files in this Project](#files-in-this-project)
* [Project Instructions](#project-instructions)

## Introduction
A startup called Sparkify wants to analyze the data they've been collecting on songs and user activity on their new music streaming app. The analytics team is particularly interested in understanding what songs users are listening to. Currently, they don't have an easy way to query their data, which resides in a directory of JSON logs on user activity on the app, as well as a directory with JSON metadata on the songs in their app.

The goal of this project is to create a Postgres database with tables designed to optimize queries on song play analysis and as a data engineer, I will create a database schema and ETL pipeline for this analysis. I will also be able to test the database and ETL pipeline by running queries given to you by the analytics team from Sparkify and compare the results with their expected results.

## Project Description
* Use the data modeling technique with Postgres to build an ETL pipeline using Python.

* Define fact and dimension tables for a star schema for a particular analytic focus, and write an ETL pipeline that transfers data from files in two local directories into these tables in Postgres using Python and SQL.

## Dataset
Two sets of data are presented in the data folder: the **Song Dataset** and the **Log Dataset**:

### Song Dataset
The first dataset is a subset of real data from the [Million Song Dataset](https://labrosa.ee.columbia.edu/millionsong/). Each file is in JSON format and contains metadata about a song and the artist of that song. The files are partitioned by the first three letters of each song's track ID. For example, here are filepaths to two files in this dataset.

```
song_data/A/B/C/TRABCEI128F424C983.json
song_data/A/A/B/TRAABJL12903CDCF1A.json
```

And below is an example of what a single song file, **TRAABJL12903CDCF1A.json**, looks like.

```json
{"num_songs": 1, "artist_id": "ARJIE2Y1187B994AB7", "artist_latitude": null, "artist_longitude": null, "artist_location": "", "artist_name": "Line Renaud", "song_id": "SOUPIRU12A6D4FA1E1", "title": "Der Kleine Dompfaff", "duration": 152.92036, "year": 0}
```

### Log Dataset
The second dataset consists of log files in JSON format generated by this [event simulator](https://github.com/Interana/eventsim) based on the songs in the dataset above. These simulate activity logs from a music streaming app based on specified configurations.

The log files in the dataset are partitioned by year and month. For example, here are filepaths to two files in this dataset.

```
log_data/2018/11/2018-11-12-events.json
log_data/2018/11/2018-11-13-events.json
```

And below is an example of what the data in a log file, **2018-11-12-events.json**, looks like.

![Log-data image](/images/log-data.png)

The JSON data within log_data files can be displayed by the creation of a pandas dataframe. JSON and pandas libraries need to be imported first.

```python
df = pd.read_json(filepath, lines=True)
```

For example,
```python
df = pd.read_json('data/log_data/2018/11/2018-11-01-events.json', lines=True)
```
would read the data file **2018-11-01-events.json**.

## Database and the Star Schema Design
Using the song and log datasets, a star schema optimized for queries on song play analysis was created based on the following entity-relationship (ER) diagram:
![ER Diagram](/images/ERD.png)

This includes the following tables:
### Fact Table
1. **songplays** - records in log data associated with song plays i.e. records with page `NextSong`
    * *songplay_id, start_time, user_id, level, song_id, artist_id, session_id, location, user_agent*
### Dimension Tables
1. **users** - users in the app
    * *user_id, first_name, last_name, gender, level*
2. **songs** - songs in music database
    * *song_id, title, artist_id, year, duration*
3. **artists** - artists in music database
    * *artist_id, name, location, latitude, longitude*
4. **time** - timestamps of records in **songplays** broken down into specific units
    * *start_time, hour, day, week, month, year, weekday*


## Files in this Project
In addition to the data files, the project workspace includes six files:

1. `test.ipynb` displays the first few rows of each table. This allows us to check the validity of the database.

2. `create_tables.py` drops and creates the tables. We can run this file to reset the tables before each time you run the ETL scripts.

3. `etl.ipynb` reads and processes a single file from song_data and log_data and loads the data into the tables. This notebook contains detailed instructions on the ETL process for each of the tables.

4. `etl.py` reads and processes files from song_data and log_data and loads them into the tables.

5. `sql_queries.py` contains all the sql queries, and is imported into the last three files above.

6. `README.md` provides discussion on this project.

## Project Instructions
1. Run `create_tables.py` to reset the tables.

    What this python script does is to establishes connection, drops all the tables, creates all tables needed, and finally, closes the connection, with the following command:

    ```python
    cur, conn = create_database()
    
    drop_tables(cur, conn)
    create_tables(cur, conn)

    conn.close()
    ```

    The drop_tables() and create_tables() functions have individual list of queries to execute which are imported from `sql_queries.py` where all the queries are located.

    ```sql
    create_table_queries = [songplay_table_create, user_table_create, song_table_create, artist_table_create, time_table_create]
    drop_table_queries = [songplay_table_drop, user_table_drop, song_table_drop, artist_table_drop, time_table_drop]
    ```

2. Run `etl.py` where the ETL processes for each table is developed.

    Five tables, according the the ER diagram, are created. Here is just a highlight of the ETL process. For complete steps with description, please refer to `etl.ipynb`.

    ### Extract Process

    2-1. Function `get_files` is defined to list the path of all the files in JSON format which will be extracted:

    ```python
    def get_files(filepath):
    all_files = []
    for root, dirs, files in os.walk(filepath):
        files = glob.glob(os.path.join(root,'*.json'))
        for f in files :
            all_files.append(os.path.abspath(f))
    
    return all_files
    ```

    2-2. Files are then loaded into a pandas DataFrame:

    ```python
    song_files = get_files('./data/song_data')
    df = pd.read_json(filepath, lines=True)
    ```

    ### Transform Process

    2-3. For a simple dataset the approach is just to select the columns to be loaded. For example, 5 columns (out of 10) are selected using the `df.values` method:

    ```python
    song_data = df[['song_id', 'title', 'artist_id', 'year', 'duration']].values.tolist()[0]
    ```

    2-4. For a more complex dataset, extra measures need to be taken in order to get the desired data. The **time** table, for example, has to go through the following steps from the timestamp in the column `ts` to get the formatted time:

    - First we have to filter the `NextSong` action:
        ```python
        df['page'] == 'NextSong'
        ```

    - Next, convert the `ts` timestamp column in milliseconds to datetime:
        ```python
        t = pd.to_datetime(df['ts'], unit='ms')
        ```
    
    - Extract the timestamp, hour, day, week of year, month, year, and weekday from the ts column and set time_data to a list containing these values in order:
        ```python
        time_data = df['ts'].tolist(),t.dt.hour.values.tolist(), t.dt.day.values.tolist(), \
            t.dt.week.values.tolist(),t.dt.month.values.tolist(), t.dt.year.values.tolist(), \
            t.dt.weekday.values.tolist()
        ```
    
    - Specify labels for these columns and set to column_labels:
        ```python
        column_labels = ('Timestamp','hour', 'day','week','month','year','weekday')
        ```
    - Create a dataframe, time_df, containing the time data for this file by combining column_labels and time_data into a dictionary and converting this into a dataframe:
        ```python
        time_df = pd.DataFrame(list(time_data), index = list(column_labels)).transpose()
        ```

    ### Load Process

    2-5. Insert the transformed data into the table. Here is how to insert the data `song_data` into the table named **songs** using the sql query shown in the next paragraph:

    ```python
    cur.execute(song_table_insert, song_data)
    conn.commit()
    ```

    ```sql
    song_table_insert = ("""
    INSERT INTO songs (
        song_id, title, artist_id, year, duration
    )
    VALUES (%s, %s, %s, %s, %s)
    ON CONFLICT (song_id) DO NOTHING;
    """)


3. (Optional) Run `test.ipynb` to confirm the records were successfully inserted into each table.

