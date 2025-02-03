# Spotify Advanced SQL Project and Query Optimization P2
Project Category: Advanced

![Spotify Logo](https://github.com/Mubasher-Rashidd/SQL-spotify-P2/blob/main/spotify_logo.jpg?raw=true)

## Overview
This project involves analyzing a Spotify dataset containing various attributes about tracks, albums, and artists using **SQL**. It covers an end-to-end process, from normalizing a denormalized dataset to performing SQL queries of varying complexity (easy, medium, and advanced), and optimizing query performance. The primary goals of the project are to practice advanced SQL skills and generate valuable insights from the dataset.

### Dataset Structure
The dataset includes the following attributes:

- **artist**: The performer of the track.
- **track**: The name of the song.
- **album**: The album to which the track belongs.
- **album_type**: The type of album (e.g., single, album).
- Various performance metrics like **danceability**, **energy**, **loudness**, **tempo**, and more.

### SQL Table Schema
```sql
-- create table
DROP TABLE IF EXISTS spotify;
CREATE TABLE spotify (
    artist VARCHAR(255),
    track VARCHAR(255),
    album VARCHAR(255),
    album_type VARCHAR(50),
    danceability FLOAT,
    energy FLOAT,
    loudness FLOAT,
    speechiness FLOAT,
    acousticness FLOAT,
    instrumentalness FLOAT,
    liveness FLOAT,
    valence FLOAT,
    tempo FLOAT,
    duration_min FLOAT,
    title VARCHAR(255),
    channel VARCHAR(255),
    views FLOAT,
    likes BIGINT,
    comments BIGINT,
    licensed BOOLEAN,
    official_video BOOLEAN,
    stream BIGINT,
    energy_liveness FLOAT,
    most_played_on VARCHAR(50)
);
```

## Project Steps

### 1. Data Exploration
Before diving into SQL queries, it's essential to thoroughly understand the dataset. Explore the attributes such as **artist**, **track**, **album**, **album_type**, and performance metrics like **danceability**, **energy**, **loudness**, and **tempo**.

### 2. Querying the Data
After populating the database, we write SQL queries to explore and analyze the data. Queries are categorized into **easy**, **medium**, and **advanced** levels.

#### Easy Queries
These involve simple data retrieval, filtering, and basic aggregations:
- Retrieve tracks with more than 1 billion streams.
- List all albums and their respective artists.
- Count total comments for tracks marked as licensed.
- Find all tracks that belong to the album type single.
- Count the total number of tracks by each artist.

#### Medium Queries
These include grouping, aggregations, and joins:
- Calculate the average danceability for tracks in each album.
- Find the top 5 tracks with the highest energy values.
- Retrieve tracks along with their views and likes for official videos.
- List all tracks along with their views and likes where official_video = TRUE.
- For each album, calculate the total views of all associated tracks.

#### Advanced Queries
These queries involve subqueries, window functions, and CTEs:
- Use window functions to find the top 3 most-viewed tracks for each artist.
- Identify tracks with above-average liveness scores.
- Use a `WITH` clause to calculate the difference between the highest and lowest energy values for tracks in each album.
- Calculate the difference between the highest and lowest energy values for tracks in each album.
- Calculate the cumulative sum of likes for tracks ordered by the number of views, using window functions.

### 3. Practice Questions

#### Easy Level
1. Retrieve the names of all tracks with more than 1 billion streams.
```sql
SELECT track FROM spotify WHERE stream > 1000000000;
```
2. List all albums with their respective artists.
```sql
SELECT album, artist FROM spotify;
```
3. Calculate the total number of comments for tracks where `licensed = TRUE`.
```sql
SELECT sum(comments) FROM spotify WHERE licensed = 'True';
```
4. Find tracks with album type `single`.
```sql
SELECT track FROM spotify WHERE album_type = 'single';
```
5. Count the total number of tracks by each artist.
```sql
SELECT artist, COUNT(track) AS total_by_each_artist FROM spotify GROUP BY artist;
```

#### Medium Level
1. Calculate the average danceability of tracks in each album.
```sql
SELECT album, AVG(danceability) AS average_value FROM spotify GROUP BY album ORDER BY average_value DESC;
```
2. Find the top 5 tracks with the highest energy values.
```sql
SELECT track, MAX(energy) AS high_energy FROM spotify GROUP BY track ORDER BY high_energy DESC LIMIT 5;
```
3. List tracks with their views and likes where `official_video = TRUE`.
```sql
SELECT track, SUM(views) AS total_views, SUM(likes) AS total_likes FROM spotify WHERE official_video = 'True' GROUP BY track ORDER BY total_views DESC LIMIT 5;
```
4. For each album, calculate the total views of all associated tracks.
```sql
SELECT album, track, SUM(views) AS total_views FROM spotify GROUP BY album, track;
```
5. Retrieve tracks that have been streamed more on Spotify than YouTube.
```sql
SELECT * FROM 
(SELECT track, COALESCE(SUM(CASE WHEN most_played_on = 'Youtube' THEN stream END),0) AS youtube, 
COALESCE(SUM(CASE WHEN most_played_on = 'Spotify' THEN stream END),0) AS spotifyy FROM spotify GROUP BY track) AS t1
WHERE spotifyy > youtube AND youtube <> 0;
```

#### Advanced Level
1. **Find the top 3 most-viewed tracks for each artist using window functions.**
```sql
WITH top_artists AS
(SELECT artist, track, SUM(views) AS total_views, DENSE_RANK() OVER (PARTITION BY artist ORDER BY SUM(views) DESC) AS rank
FROM spotify GROUP BY artist, track)
SELECT * FROM top_artists WHERE rank <= 3;
```
2. **Retrieve tracks where the liveness score is above average.**
```sql
SELECT track, liveness FROM spotify WHERE liveness > (SELECT AVG(liveness) AS avg_liveness FROM spotify);
```
3. **Calculate the difference between the highest and lowest energy values for tracks in each album.**
```sql
WITH outt AS
(SELECT album, MAX(energy) AS highest, MIN(energy) AS lowest FROM spotify GROUP BY album)
SELECT album, highest - lowest AS total FROM outt ORDER BY total DESC;
```
4. **Find tracks with an energy-to-liveness ratio greater than 1.2.**
```sql
SELECT * FROM (SELECT track, energy, liveness, (energy/liveness) AS ratio FROM spotify) WHERE ratio > 1.2;
```
5. **Calculate the cumulative sum of likes for tracks ordered by the number of views, using window functions.**
```sql
SELECT track, likes, views, 
       SUM(likes) OVER (ORDER BY views DESC) AS cumulative_likes
FROM spotify
ORDER BY views DESC;
```

---

### 4. Query Optimization

To optimize query performance, we focus on the following techniques:

#### Indexing
- **Create Index on Frequently Queried Columns**: For instance, indexing the **artist** column speeds up query retrieval.
  
#### Execution Plan Analysis
- Use `EXPLAIN ANALYZE` to review and optimize query performance.

#### Query Execution Plan Before and After Optimization:
- **Before Indexing**: Execution time (E.T.): **7 ms**, Planning time (P.T.): **0.17 ms**.
  
- **After Indexing**: Execution time (E.T.): **0.153 ms**, Planning time (P.T.): **0.152 ms**.

#### Visual Performance Comparison
- **Graphical comparison** showing a significant reduction in execution time after indexing.

![EXPLAIN Before Index](https://github.com/Mubasher-Rashidd/SQL-spotify-P2/blob/main/spotify_explain_before_index.png?raw=true)
![EXPLAIN After Index](https://github.com/Mubasher-Rashidd/SQL-spotify-P2/blob/main/spotify_explain_after_index.png?raw=true)
![Performance Graph](https://github.com/Mubasher-Rashidd/SQL-spotify-P2/blob/main/spotify_graphical%20view%203.png?raw=true)

---

## Technology Stack
- **Database**: PostgreSQL
- **SQL Queries**: DDL, DML, Aggregations, Joins, Subqueries, Window Functions
- **Tools**: pgAdmin 4 (or any SQL editor), PostgreSQL (via Homebrew, Docker, or direct installation)

## How to Run the Project
1. Install PostgreSQL and pgAdmin (if not already installed).
2. Set up the database schema and tables using the provided normalization structure.
3. Insert the sample data into the respective tables.
4. Execute SQL queries to solve the listed problems.
5. Explore query optimization techniques for large datasets.

---

## Next Steps
- **Visualize the Data**: Use data visualization tools like **Tableau** or **Power BI** to create dashboards based on the query results.
- **Expand Dataset**: Add more rows to the dataset for broader analysis and scalability testing.
- **Advanced Querying**: Dive deeper into query optimization and explore the performance of SQL queries on larger datasets.

---

## Contributing
If you'd like to contribute to this project, feel free to fork the repository, submit pull requests, or raise issues.

---
