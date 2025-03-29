# Analysing Spotify data by using SQL

# Objectives
The objective of this project is to analyze the `spotify` dataset using SQL queries to extract meaningful insights about artists, tracks, albums, and various musical attributes. By solving a range of problems from basic data retrieval to advanced analytical queries, the project aims to explore trends in song popularity, engagement metrics, and audio characteristics. The analysis will help in understanding factors influencing music streaming success, audience preferences, and correlations between different musical elements.

# Data
The data for this project can be found [here](https://www.kaggle.com/datasets/sanjanchaudhari/spotify-dataset) on Kaggle

# Problems to solve
This project consists of 20 SQL problems designed to analyze and extract insights from the `spotify` dataset. The problems are categorized into three levels based on complexity: 5 easy problems focus on basic data retrieval and filtering, 10 intermediate problems involve aggregations, rankings, and conditional queries, while 5 advanced problems require complex calculations, correlations, and deeper analysis of musical trends. These challenges will help uncover patterns in song popularity, engagement, and streaming behavior.

# Easy Problems
1. Retrieve all tracks and their respective artists.
   
```sql
SELECT
   track,
   artist
FROM spotify;
```


2. Find all tracks from the album titled "Thriller."

```sql
SELECT
   track
FROM spotify
WHERE album = 'Thriller';
```
 
3. List distinct album types available in the dataset.

```sql
SELECT 
   DISTINCT(album)
FROM spotify;
```   
4. Retrieve the top 10 most-viewed tracks.

```sql
SELECT
   track,
   view
FROM spotify
ORDER BY view DESC
LIMIT 10;
```
5. Find the total number of tracks in the dataset.  

```sql
SELECT
   COUNT(track) AS total_tracks
FROM spotify;
```

# Intermediate Problems
6. Find the average duration (in minutes) of songs by each artist.

```sql
SELECT
	artist,
	AVG(duration_min) AS avg_duration
FROM spotify
GROUP BY artist;
```

7. Retrieve the top 5 artists with the highest total streams.  
9. Find all songs where `danceability` is greater than 0.8 and `energy` is greater than 0.7.  
10. Calculate the average `valence` (musical positivity) for each album.  
11. List all albums with more than 1 million total streams.  
12. Find the artist whose songs have received the most likes.  
13. Retrieve the top 3 most-played tracks on each platform (`most_played_on`).  
14. Count the number of songs that have an official video.  
15. List all tracks where `liveness` is above 0.7 but `acousticness` is below 0.3.  
15. Find all tracks that are completely instrumental (`instrumentalness` = 1).  

# Advanced Problems
16. Determine the artist with the highest engagement rate, defined as (likes + comments) per view.Note there shouldn't be null values in engagement rate.

```sql
SELECT
	artist,
	engagement_rate
FROM (
		SELECT
			artist,
			sum(likes) as total_likes,
			sum(comments) as total_comments,
			ROUND((sum(likes)+sum(comments)) / NULLIF(sum(views),0),4) as engagement_rate
		FROM spotify
		GROUP BY artist
		)
WHERE engagement_rate IS NOT NULL
ORDER BY engagement_rate DESC
LIMIT 1;
```

17. Find the top 3 most streamed songs for the album 'Greatest Hits'

```sql
WITH top_3 AS (
		SELECT
			album,
			track,
			stream,
			RANK() OVER(PARTITION BY album ORDER BY stream DESC) AS top_3_steamed
		FROM spotify
		ORDER BY album
)
SELECT
	album,
	track,
	stream,
	top_3_steamed
FROM top_3
WHERE top_3_steamed <= 3 AND album = 'Greatest Hits';
```


18. Which albums (excluding null values) have more than 5 unique tracks and a total of more than 2 Billion views across all their tracks?.

```sql
WITH album_stats AS (
    SELECT 
        DISTINCT(album) AS album,
        COUNT(DISTINCT(track)) AS track_count,
        SUM(views) AS total_views
    FROM spotify
	WHERE album IS NOT NULL
    GROUP BY album
)
SELECT 
    album,
    track_count,
    total_views
FROM album_stats
WHERE track_count > 5 
AND total_views > 2000000000;
```
     
19. Calculate the correlation between `danceability` and `energy` for all songs.

# Correlation = (nΣxy - (Σx)(Σy)) / (sqrt(nΣx² - (Σx)²) * sqrt(nΣy² - (Σy)²)) #
Where:

-n is the number of observations
-x is danceability
*y is energy
*Σxy is the sum of the product of danceability and energy
*Σx and Σy are the sums of danceability and energy respectively
*Σx² and Σy² are the sums of squared danceability and energy respectively

The result will be a value between -1 and 1, where:

*1 indicates a perfect positive correlation
*-1 indicates a perfect negative correlation
*0 indicates no correlation

```sql
SELECT 
    (COUNT(*) * SUM(Danceability * Energy) - SUM(Danceability) * SUM(Energy)) / 
    (SQRT(COUNT(*) * SUM(Danceability * Danceability) - SUM(Danceability) * SUM(Danceability)) * 
     SQRT(COUNT(*) * SUM(Energy * Energy) - SUM(Energy) * SUM(Energy))) AS Correlation
FROM spotify;
```

21. Identify artists whose total number of songs in the dataset exceeds the average number of songs per artist.  

# Platform
For this project, we are using **PostgreSQL** as the database management system to store and analyze the `spotify` dataset. PostgreSQL provides powerful SQL capabilities, making it ideal for executing complex queries, performing aggregations, and extracting meaningful insights from the data efficiently.


