# data-modeling-with-cassandra

# Modeling Sparkify's user and song activity data with Apache Cassandra

## Overview
Sparkify is a new startup that has built a new music streaming app. The Sparkify analytics team wants to understand what songs their users are listening to. At present, they don't have an easy way to query their data given that it is housed in directories of CSV files. These CSV files contain user and song activity on the app. This projects allows Sparkify's analytics team to easily query their data by providing them with a Cassandra database containing tables designed to optimize queries on song play and user analysis.

## Database Schema
Sparkify's Cassandra database contains three tables, each optimized to run a specific query.
1. music_by_session_id_and_item: The music_by_session_id_and_item table uses session_id as its partition key and item_in_session as its clustering column. This allows us to query the table by session_id and will ensure we receive songs in the order in which they were played.
2. music_by_user_id_and_session_id: The music_by_user_id_and_session_id table uses (user_id, session_id) as it's composite key so that data is partitioned by user_id and session_id. This ensures that we can query the data by user_id and session_id. Since we have used item_in_session as the clustering column the songs will be returned in the order they were played.
3. user_by_song: The user_by_song table uses song_title as the partition key and user_id as the clustering column. This allows us to query on song_title and have the results ordered by user_id.

## ETL process
Multiple CSVs of Sparkify's user and song data sit in a directory. Those CSVs are transformed into a single streamlined CSV containing all relevant data. The CSV is then read into a Pandas dataframe and inserted into the relevant Cassandra tables.

Here is an example of the python code that processes the CSVs into a single, streamlined CSV.

``full_data_rows_list = []

for f in file_path_list:
    with open(f, 'r', encoding = 'utf8', newline='') as csvfile:
        # creating a csv reader object
        csvreader = csv.reader(csvfile)
        next(csvreader)     
        for line in csvreader:
            #print(line)
            full_data_rows_list.append(line)

csv.register_dialect('myDialect', quoting=csv.QUOTE_ALL, skipinitialspace=True)
with open('event_datafile_new.csv', 'w', encoding = 'utf8', newline='') as f:
    writer = csv.writer(f, dialect='myDialect')
    writer.writerow(['artist','firstName','gender','itemInSession','lastName','length',\
                'level','location','sessionId','song','userId'])
    for row in full_data_rows_list:
        if (row[0] == ''):
            continue
        writer.writerow((row[0], row[2], row[3], row[4], row[5], row[6], row[7], row[8], row[12], row[13], row[16]))``

## Technologies used
1. Python 3
2. Pandas
3. Apache Cassandra

## Files
- event_datafile_new.csv This is the streamlined CSV file containing the data that will be inserted into the tables
- Cassandra_data_modeling.ipynb This is a Jupyter file that process the data and inserts it into the tables

## Runnig the scripts
1. Open Cassandra_data_modeling.ipynb and run all cells


#### Credits
Code written by Scott Millslagle and Udacity
