# Data Modeling with Postgres
>
Summary of this project

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

![Log-data image](/log-data.png)

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
Using the song and log datasets, a star schema optimized for queries on song play analysis was created based on the following entity relationship diagram:

## Files in this Project
Detail of this section

## Project Instructions
Detail of this section

