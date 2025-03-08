# project04_DEC_K14
-- Q1. Find the total number of rows in each table of the schema?
-- Type your code below:

mysql> SELECT COUNT(*) AS count_records FROM movie;
+---------------+
| count_records |
+---------------+
|          7997 |
+---------------+
1 row in set (0.01 sec)

mysql> SELECT COUNT(*) AS count_records FROM genre;
+---------------+
| count_records |
+---------------+
|         14662 |
+---------------+
1 row in set (0.01 sec)

mysql> SELECT COUNT(*) AS count_records FROM director_mapping;
+---------------+
| count_records |
+---------------+
|          3867 |
+---------------+
1 row in set (0.00 sec)

mysql> SELECT COUNT(*) AS count_records FROM role_mapping;
+---------------+
| count_records |
+---------------+
|         15615 |
+---------------+
1 row in set (0.00 sec)

mysql> SELECT COUNT(*) AS count_records FROM names;
+---------------+
| count_records |
+---------------+
|         25735 |
+---------------+
1 row in set (0.01 sec)


-- Q2. Which columns in the movie table have null values?
-- Type your code below:
mysql> SELECT 
    ->     SUM(CASE WHEN id IS NULL THEN 1 ELSE 0 END) AS count_null_id,
    ->     SUM(CASE WHEN title IS NULL THEN 1 ELSE 0 END) AS count_null_title,
    ->     SUM(CASE WHEN year IS NULL THEN 1 ELSE 0 END) AS count_null_year,
    ->     SUM(CASE WHEN date_published IS NULL THEN 1 ELSE 0 END) AS count_null_date_published,
    ->     SUM(CASE WHEN duration IS NULL THEN 1 ELSE 0 END) AS count_null_duration,
    ->     SUM(CASE WHEN country IS NULL THEN 1 ELSE 0 END) AS count_null_country,
    ->     SUM(CASE WHEN worlwide_gross_income IS NULL THEN 1 ELSE 0 END) AS count_null_worlwide_gross_income,
    ->     SUM(CASE WHEN languages IS NULL THEN 1 ELSE 0 END) AS count_null_languages,
    ->     SUM(CASE WHEN production_company IS NULL THEN 1 ELSE 0 END) AS count_null_production_company
    -> FROM movie;
+---------------+------------------+-----------------+---------------------------+---------------------+--------------------+----------------------------------+----------------------+-------------------------------+
| count_null_id | count_null_title | count_null_year | count_null_date_published | count_null_duration | count_null_country | count_null_worlwide_gross_income | count_null_languages | count_null_production_company |
+---------------+------------------+-----------------+---------------------------+---------------------+--------------------+----------------------------------+----------------------+-------------------------------+
|             0 |                0 |               0 |                         0 |                   0 |                 20 |                             3724 |                  194 |                           528 |
+---------------+------------------+-----------------+---------------------------+---------------------+--------------------+----------------------------------+----------------------+-------------------------------+
1 row in set (0.03 sec)

-- Now as you can see four columns of the movie table has null values. Let's look at the at the movies released each year. 
-- Q3. Find the total number of movies released each year? How does the trend look month wise? (Output expected)

--Output format for the first part:
mysql> SELECT
    -> year,
    -> COUNT(id) AS number_of_movies 
    -> FROM movie
    -> GROUP BY year
    -> ORDER BY year;
+------+------------------+
| year | number_of_movies |
+------+------------------+
| 2017 |             3052 |
| 2018 |             2944 |
| 2019 |             2001 |
+------+------------------+
3 rows in set (0.01 sec)

-- Output format for the second part of the question:
mysql> SELECT
    ->     MONTH(date_published) AS month,
    ->     COUNT(id) AS number_of_movies
    -> FROM movie
    -> GROUP BY MONTH(date_published)
    -> ORDER BY month;
+-------+------------------+
| month | number_of_movies |
+-------+------------------+
|     1 |              804 |
|     2 |              640 |
|     3 |              824 |
|     4 |              680 |
|     5 |              625 |
|     6 |              580 |
|     7 |              493 |
|     8 |              678 |
|     9 |              809 |
|    10 |              801 |
|    11 |              625 |
|    12 |              438 |
+-------+------------------+
12 rows in set (0.01 sec)

-- Q4. How many movies were produced in the USA or India in the year 2019??
-- Type your code below:

mysql> SELECT
    -> COUNT(id) AS number_of_movies
    -> FROM movie
    -> WHERE year = 2019 AND (country = 'USA' OR country = 'India');
+------------------+
| number_of_movies |
+------------------+
|              887 |
+------------------+
1 row in set (0.00 sec)



-- Q5. Find the unique list of the genres present in the data set?
-- Type your code below:

mysql> SELECT DISTINCT genre
    -> FROM genre;
+-----------+
| genre     |
+-----------+
| Drama     |
| Fantasy   |
| Thriller  |
| Comedy    |
| Horror    |
| Family    |
| Romance   |
| Adventure |
| Action    |
| Sci-Fi    |
| Crime     |
| Mystery   |
| Others    |
+-----------+
13 rows in set (0.02 sec)


-- Q6.Which genre had the highest number of movies produced overall?
-- Type your code below:
mysql> SELECT
    -> genre,
    -> COUNT(DISTINCT movie_id) AS number_of_movie
    -> FROM genre
    -> GROUP BY genre
    -> ORDER BY number_of_movie DESC;
+-----------+-----------------+
| genre     | number_of_movie |
+-----------+-----------------+
| Drama     |            4285 |
| Comedy    |            2412 |
| Thriller  |            1484 |
| Action    |            1289 |
| Horror    |            1208 |
| Romance   |             906 |
| Crime     |             813 |
| Adventure |             591 |
| Mystery   |             555 |
| Sci-Fi    |             375 |
| Fantasy   |             342 |
| Family    |             302 |
| Others    |             100 |
+-----------+-----------------+
13 rows in set (0.04 sec)


-- Q7. How many movies belong to only one genre?
-- Type your code below:
mysql> SELECT COUNT(*)
    -> FROM (  SELECT
    ->         movie_id, COUNT(DISTINCT genre) AS count_genre
    ->         FROM genre
    ->         GROUP BY movie_id
    ->         HAVING COUNT(DISTINCT genre) = 1) t1;
+----------+
| COUNT(*) |
+----------+
|     3289 |
+----------+
1 row in set (0.02 sec)


-- Q8.What is the average duration of movies in each genre? 
-- (Note: The same movie can belong to multiple genres.)

-- Kiểm tra genre có bị duplicate không
SELECT COUNT(*) FROM genre -- 14662
SELECT COUNT(*) FROM (SELECT DISTINCT * FROM genre) AS subquery; -- 14662

mysql> SELECT
    -> g.genre,
    -> ROUND(AVG(m.duration),0) AS avg_duration
    -> FROM movie m
    -> INNER JOIN genre g ON g.movie_id = m.id
    -> GROUP BY g.genre;
+-----------+--------------+
| genre     | avg_duration |
+-----------+--------------+
| Drama     |          107 |
| Fantasy   |          105 |
| Thriller  |          102 |
| Comedy    |          103 |
| Horror    |           93 |
| Family    |          101 |
| Romance   |          110 |
| Adventure |          102 |
| Action    |          113 |
| Sci-Fi    |           98 |
| Crime     |          107 |
| Mystery   |          102 |
| Others    |          100 |
+-----------+--------------+
13 rows in set (0.06 sec)


-- Q9.What is the rank of the ‘thriller’ genre of movies among all the genres in terms of number of movies produced? 
-- (Hint: Use the Rank function)

mysql> SELECT *
    -> FROM (SELECT
    -> t1.*,
    -> DENSE_RANK() OVER (ORDER BY number_of_movie DESC) AS genre_rank
    -> FROM (SELECT
    -> genre,
    -> COUNT(DISTINCT movie_id) AS number_of_movie
    -> FROM genre
    -> GROUP BY genre) AS t1) AS t2
    -> WHERE genre = 'Thriller';
+----------+-----------------+------------+
| genre    | number_of_movie | genre_rank |
+----------+-----------------+------------+
| Thriller |            1484 |          3 |
+----------+-----------------+------------+
1 row in set (0.05 sec)


-- Q10.  Find the minimum and maximum values in  each column of the ratings table except the movie_id column?
mysql> SELECT
    -> MIN(avg_rating) AS min_avg_rating,
    -> MAX(avg_rating) AS max_avg_rating,
    -> MIN(total_votes) AS min_total_votes,
    -> MAX(total_votes) AS max_total_votes,
    -> MIN(median_rating) AS min_median_rating,
    -> MAX(median_rating) AS max_median_rating
    -> FROM ratings;
+----------------+----------------+-----------------+-----------------+-------------------+-------------------+
| min_avg_rating | max_avg_rating | min_total_votes | max_total_votes | min_median_rating | max_median_rating |
+----------------+----------------+-----------------+-----------------+-------------------+-------------------+
|            1.0 |           10.0 |             100 |          725138 |                 1 |                10 |
+----------------+----------------+-----------------+-----------------+-------------------+-------------------+
1 row in set (0.01 sec)


-- Q11. Which are the top 10 movies based on average rating?
mysql> SELECT *
    -> FROM (SELECT
    -> m.title,
    -> r.avg_rating,
    -> DENSE_RANK() OVER (ORDER BY r.avg_rating DESC) AS moving_rank
    -> FROM ratings r
    -> INNER JOIN movie m ON r.movie_id = m.id) t1
    -> WHERE moving_rank <= 10;
+-----------------------------------------------------------------+------------+-------------+
| title                                                           | avg_rating | moving_rank |
+-----------------------------------------------------------------+------------+-------------+
| Kirket                                                          |       10.0 |           1 |
| Love in Kilnerry                                                |       10.0 |           1 |
| Gini Helida Kathe                                               |        9.8 |           2 |
| Runam                                                           |        9.7 |           3 |
| Fan                                                             |        9.6 |           4 |
| Android Kunjappan Version 5.25                                  |        9.6 |           4 |
| Yeh Suhaagraat Impossible                                       |        9.5 |           5 |
| Safe                                                            |        9.5 |           5 |
| The Brighton Miracle                                            |        9.5 |           5 |
| Shibu                                                           |        9.4 |           6 |
| Our Little Haven                                                |        9.4 |           6 |
| Zana                                                            |        9.4 |           6 |
| Family of Thakurganj                                            |        9.4 |           6 |
| Ananthu V/S Nusrath                                             |        9.4 |           6 |
| Eghantham                                                       |        9.3 |           7 |
| Wheels                                                          |        9.3 |           7 |
| Turnover                                                        |        9.2 |           8 |
| Digbhayam                                                       |        9.2 |           8 |
| Tõde ja õigus                                                   |        9.2 |           8 |
| Ekvtime: Man of God                                             |        9.2 |           8 |
| Leera the Soulmate                                              |        9.2 |           8 |
| AA BB KK                                                        |        9.2 |           8 |
| Peranbu                                                         |        9.2 |           8 |
| Dokyala Shot                                                    |        9.2 |           8 |
| Ardaas Karaan                                                   |        9.2 |           8 |
| Kuasha jakhon                                                   |        9.1 |           9 |
| Oththa Seruppu Size 7                                           |        9.1 |           9 |
| Adutha Chodyam                                                  |        9.1 |           9 |
| The Colour of Darkness                                          |        9.1 |           9 |
| Aloko Udapadi                                                   |        9.1 |           9 |
| C/o Kancharapalem                                               |        9.1 |           9 |
| Nagarkirtan                                                     |        9.1 |           9 |
| Jelita Sejuba: Mencintai Kesatria Negara                        |        9.1 |           9 |
| Shindisi                                                        |        9.0 |          10 |
| Officer Arjun Singh IPS                                         |        9.0 |          10 |
| Oskars Amerika                                                  |        9.0 |          10 |
| Delaware Shore                                                  |        9.0 |          10 |
| Abstruse                                                        |        9.0 |          10 |
| National Theatre Live: Angels in America Part Two - Perestroika |        9.0 |          10 |
| Innocent                                                        |        9.0 |          10 |
+-----------------------------------------------------------------+------------+-------------+
40 rows in set (0.06 sec)



/* Do you find you favourite movie FAN in the top 10 movies with an average rating of 9.6? If not, please check your code again!!
So, now that you know the top 10 movies, do you think character actors and filler actors can be from these movies?
Summarising the ratings table based on the movie counts by median rating can give an excellent insight.*/

-- Q12. Summarise the ratings table based on the movie counts by median ratings.
mysql> SELECT
    -> median_rating,
    -> COUNT(movie_id) AS movie_count
    -> FROM ratings
    -> GROUP BY median_rating
    -> ORDER BY median_rating;
+---------------+-------------+
| median_rating | movie_count |
+---------------+-------------+
|             1 |          94 |
|             2 |         119 |
|             3 |         283 |
|             4 |         479 |
|             5 |         985 |
|             6 |        1975 |
|             7 |        2257 |
|             8 |        1030 |
|             9 |         429 |
|            10 |         346 |
+---------------+-------------+
10 rows in set (0.01 sec)


-- Q13. Which production house has produced the most number of hit movies (average rating > 8)??
mysql> SELECT
    -> *
    -> FROM (SELECT
    -> *,
    -> DENSE_RANK() OVER (ORDER BY movie_count DESC) AS prod_company_rank
    -> FROM (SELECT
    -> m.production_company,
    -> COUNT(m.id) AS movie_count
    -> FROM movie m
    -> INNER JOIN ratings r ON r.movie_id = m.id AND r.avg_rating > 8 AND m.production_company IS NOT NULL
    -> GROUP BY m.production_company) AS t1) AS t1
    -> WHERE prod_company_rank = 1;
+------------------------+-------------+-------------------+
| production_company     | movie_count | prod_company_rank |
+------------------------+-------------+-------------------+
| Dream Warrior Pictures |           3 |                 1 |
| National Theatre Live  |           3 |                 1 |
+------------------------+-------------+-------------------+
2 rows in set (0.02 sec)


-- Q14. How many movies released in each genre during March 2017 in the USA had more than 1,000 votes?
mysql> SELECT
    -> g.genre,
    -> COUNT(g.movie_id) AS movie_count
    -> FROM genre g
    -> INNER JOIN movie m ON g.movie_id = m.id AND m.year =  2017 AND MONTH(m.date_published) = 3 AND country = 'USA'
    -> INNER JOIN ratings r ON g.movie_id = r.movie_id AND r.total_votes > 1000
    -> GROUP BY g.genre
    -> ORDER BY movie_count DESC;
+----------+-------------+
| genre    | movie_count |
+----------+-------------+
| Drama    |          16 |
| Comedy   |           8 |
| Crime    |           5 |
| Horror   |           5 |
| Action   |           4 |
| Sci-Fi   |           4 |
| Thriller |           4 |
| Romance  |           3 |
| Fantasy  |           2 |
| Mystery  |           2 |
| Family   |           1 |
+----------+-------------+
11 rows in set (0.02 sec)


-- Q15. Find movies of each genre that start with the word ‘The’ and which have an average rating > 8?
mysql> SELECT
    ->     m.title,
    ->     r.avg_rating,
    ->     g.genre
    -> FROM movie m
    -> INNER JOIN ratings r ON m.id = r.movie_id AND r.avg_rating > 8
    -> INNER JOIN genre g ON m.id = g.movie_id
    -> WHERE m.title LIKE 'The%'  -- Thay ILIKE bằng LIKE
    -> ORDER BY g.genre;
+--------------------------------------+------------+----------+
| title                                | avg_rating | genre    |
+--------------------------------------+------------+----------+
| Theeran Adhigaaram Ondru             |        8.3 | Action   |
| The Irishman                         |        8.7 | Crime    |
| Theeran Adhigaaram Ondru             |        8.3 | Crime    |
| The Gambinos                         |        8.4 | Crime    |
| The Blue Elephant 2                  |        8.8 | Drama    |
| The Brighton Miracle                 |        9.5 | Drama    |
| The Irishman                         |        8.7 | Drama    |
| The Colour of Darkness               |        9.1 | Drama    |
| The Mystery of Godliness: The Sequel |        8.5 | Drama    |
| The Gambinos                         |        8.4 | Drama    |
| The King and I                       |        8.2 | Drama    |
| The Blue Elephant 2                  |        8.8 | Horror   |
| The Blue Elephant 2                  |        8.8 | Mystery  |
| The King and I                       |        8.2 | Romance  |
| Theeran Adhigaaram Ondru             |        8.3 | Thriller |
+--------------------------------------+------------+----------+
15 rows in set (0.02 sec)



-- Q16. Of the movies released between 1 April 2018 and 1 April 2019, how many were given a median rating of 8?
-- Type your code below:
mysql> SELECT
    -> COUNT(*)
    -> FROM movie m
    -> INNER JOIN ratings r ON r.movie_id = m.id AND r.median_rating = 8 AND m.date_published > '2018-04-01' AND m.date_published < '2019-04-01';
+----------+
| COUNT(*) |
+----------+
|      360 |
+----------+
1 row in set (0.02 sec)



-- Once again, try to solve the problem given below.
-- Q17. Do German movies get more votes than Italian movies? 
-- Hint: Here you have to find the total number of votes for both German and Italian movies.
-- Type your code below:
mysql> SELECT
    -> SUM(total_votes) AS sum_votes
    -> FROM movie m
    -> INNER JOIN ratings r ON m.id = r.movie_id
    -> WHERE m.country LIKE '%Germany%';
+-----------+
| sum_votes |
+-----------+
|   2026223 |
+-----------+
1 row in set (0.04 sec)

mysql> SELECT
    -> SUM(total_votes) AS sum_votes
    -> FROM movie m
    -> INNER JOIN ratings r ON m.id = r.movie_id
    -> WHERE m.country LIKE '%Italy%';
+-----------+
| sum_votes |
+-----------+
|    703024 |
+-----------+
1 row in set (0.01 sec)

-- Q18. Which columns in the names table have null values??
--Hint: You can find null values for individual columns or follow below output format

mysql> SELECT
    -> SUM(CASE WHEN id IS NULL THEN 1 ELSE 0 END) AS column_id,
    -> SUM(CASE WHEN name IS NULL THEN 1 ELSE 0 END) AS column_name,
    -> SUM(CASE WHEN height IS NULL THEN 1 ELSE 0 END) AS column_height,
    -> SUM(CASE WHEN date_of_birth IS NULL THEN 1 ELSE 0 END) AS column_dob,
    -> SUM(CASE WHEN known_for_movies IS NULL THEN 1 ELSE 0 END) AS column_kfm
    -> FROM names;
+-----------+-------------+---------------+------------+------------+
| column_id | column_name | column_height | column_dob | column_kfm |
+-----------+-------------+---------------+------------+------------+
|         0 |           0 |         17335 |      13431 |      15226 |
+-----------+-------------+---------------+------------+------------+
1 row in set (0.05 sec)

-- Q19. Who are the top three directors in the top three genres whose movies have an average rating > 8?
-- (Hint: The top three genres would have the most number of movies with an average rating > 8.)

mysql> SELECT
    -> n.name,
    -> COUNT(DISTINCT dm.movie_id) AS COUNT_movie_id
    -> FROM genre g
    -> INNER JOIN (
    -> SELECT
    -> g.genre,
    -> COUNT(DISTINCT g.movie_id) AS count_movie_id
    -> FROM genre g
    -> INNER JOIN movie m ON g.movie_id = m.id
    -> INNER JOIN ratings r ON r.movie_id = m.id AND r.avg_rating > 8
    -> GROUP BY g.genre
    -> ORDER BY count_movie_id DESC
    -> LIMIT 3) t1 ON t1.genre = g.genre
    -> INNER JOIN director_mapping dm ON dm.movie_id = g.movie_id
    -> INNER JOIN names n ON dm.name_id = n.id
    -> GROUP BY n.name
    -> ORDER BY COUNT_movie_id DESC
    -> LIMIT 3;
+------------------+----------------+
| name             | COUNT_movie_id |
+------------------+----------------+
| Sam Liu          |              4 |
| A.L. Vijay       |              4 |
| Jesse V. Johnson |              4 |
+------------------+----------------+
3 rows in set (0.10 sec)


/* James Mangold can be hired as the director for Bee's next project. Do you remeber his movies, 'Logan' and 'The Wolverine'. 
Now, let’s find out the top two actors.*/

-- Q20. Who are the top two actors whose movies have a median rating >= 8?
mysql> SELECT
    -> n.id, n.name,
    -> COUNT(DISTINCT m.id) AS count_movie
    -> FROM names n
    -> INNER JOIN role_mapping rm ON rm.name_id = n.id
    -> INNER JOIN movie m ON m.id = rm.movie_id
    -> INNER JOIN ratings r ON r.movie_id = m.id AND r.median_rating >= 8
    -> GROUP BY n.id, n.name
    -> ORDER BY count_movie DESC
    -> LIMIT 2;
+-----------+-----------+-------------+
| id        | name      | count_movie |
+-----------+-----------+-------------+
| nm0007123 | Mammootty |           8 |
| nm0482320 | Mohanlal  |           5 |
+-----------+-----------+-------------+
2 rows in set (0.07 sec)


-- Q21. Which are the top three production houses based on the number of votes received by their movies?
mysql> SELECT
    -> m.production_company,
    -> SUM(r.total_votes) AS sum_votes
    -> FROM movie m
    -> INNER JOIN ratings r ON r.movie_id = m.id
    -> GROUP BY m.production_company
    -> ORDER BY sum_votes DESC
    -> LIMIT 3;
+-----------------------+-----------+
| production_company    | sum_votes |
+-----------------------+-----------+
| Marvel Studios        |   2656967 |
| Twentieth Century Fox |   2411163 |
| Warner Bros.          |   2396057 |
+-----------------------+-----------+
3 rows in set (0.05 sec)


-- Q22. Rank actors with movies released in India based on their average ratings. Which actor is at the top of the list?
-- Note: The actor should have acted in at least five Indian movies. 
-- (Hint: You should use the weighted average based on votes. If the ratings clash, then the total number of votes should act as the tie breaker.)

mysql> SELECT
    ->     n.id, 
    ->     n.name AS name_actor,
    ->     SUM(r.total_votes) AS total_votes,
    ->     COUNT(DISTINCT m.id) AS movie_count,
    ->     ROUND(SUM(r.avg_rating * r.total_votes) / NULLIF(SUM(r.total_votes), 0), 2) AS avg_rating,
    ->     RANK() OVER (ORDER BY 
    ->         ROUND(SUM(r.avg_rating * r.total_votes) / NULLIF(SUM(r.total_votes), 0), 2) DESC, 
    ->         SUM(r.total_votes) DESC
    ->     ) AS actor_rank
    -> FROM names n
    -> INNER JOIN role_mapping rm ON rm.name_id = n.id AND rm.category = 'actor'
    -> INNER JOIN movie m ON m.id = rm.movie_id AND m.country LIKE '%India%'
    -> INNER JOIN ratings r ON r.movie_id = m.id
    -> GROUP BY n.id, n.name
    -> HAVING COUNT(DISTINCT m.id) >= 5
    -> ORDER BY avg_rating DESC, total_votes DESC
    -> LIMIT 1;
+-----------+------------------+-------------+-------------+------------+------------+
| id        | name_actor       | total_votes | movie_count | avg_rating | actor_rank |
+-----------+------------------+-------------+-------------+------------+------------+
| nm4043111 | Vijay Sethupathi |       23114 |           5 |       8.42 |          1 |
+-----------+------------------+-------------+-------------+------------+------------+
1 row in set (0.05 sec)


-- Q23.Find out the top five actresses in Hindi movies released in India based on their average ratings? 
-- Note: The actresses should have acted in at least three Indian movies. 
-- (Hint: You should use the weighted average based on votes. If the ratings clash, then the total number of votes should act as the tie breaker.)

mysql> SELECT
    ->     n.id, 
    ->     n.name AS name_actor,
    ->     SUM(r.total_votes) AS total_votes,
    ->     COUNT(DISTINCT m.id) AS movie_count,
    ->     ROUND(SUM(r.avg_rating * r.total_votes) / NULLIF(SUM(r.total_votes), 0), 2) AS avg_rating,
    ->     RANK() OVER (ORDER BY 
    ->         ROUND(SUM(r.avg_rating * r.total_votes) / NULLIF(SUM(r.total_votes), 0), 2) DESC, 
    ->         SUM(r.total_votes) DESC
    ->     ) AS actress_rank
    -> FROM names n
    -> INNER JOIN role_mapping rm ON rm.name_id = n.id AND rm.category = 'actress'
    -> INNER JOIN movie m ON m.id = rm.movie_id 
    -> AND m.country LIKE '%India%' 
    -> AND m.languages LIKE '%hindi%'
    -> INNER JOIN ratings r ON r.movie_id = m.id
    -> GROUP BY n.id, n.name
    -> HAVING COUNT(DISTINCT m.id) >= 3
    -> LIMIT 5;
+-----------+-----------------+-------------+-------------+------------+--------------+
| id        | name_actor      | total_votes | movie_count | avg_rating | actress_rank |
+-----------+-----------------+-------------+-------------+------------+--------------+
| nm3966456 | Taapsee Pannu   |       18061 |           3 |       7.74 |            1 |
| nm5305841 | Kriti Sanon     |       21967 |           3 |       7.05 |            2 |
| nm0244890 | Divya Dutta     |        8579 |           3 |       6.88 |            3 |
| nm3601766 | Shraddha Kapoor |       26779 |           3 |       6.63 |            4 |
| nm3713653 | Kriti Kharbanda |        2549 |           3 |       4.80 |            5 |
+-----------+-----------------+-------------+-------------+------------+--------------+
5 rows in set (0.02 sec)


/* Q24. Select thriller movies as per avg rating and classify them in the following category: 

			Rating > 8: Superhit movies
			Rating between 7 and 8: Hit movies
			Rating between 5 and 7: One-time-watch movies
			Rating < 5: Flop movies
--------------------------------------------------------------------------------------------*/

mysql> SELECT 
    -> g.movie_id, g.genre,
    -> r.avg_rating,
    -> CASE
    -> WHEN r.avg_rating > 8 THEN 'Superhit movies'
    -> WHEN r.avg_rating >= 7 THEN 'Hit movies'
    -> WHEN r.avg_rating >= 5 THEN 'One-time-watch movies'
    -> ELSE 'Flop movies'
    -> END category
    -> FROM genre g
    -> INNER JOIN ratings r ON r.movie_id = g.movie_id
    -> WHERE genre = 'Thriller';


/* Until now, you have analysed various tables of the data set. 
Now, you will perform some tasks that will give you a broader understanding of the data in this segment.*/

-- Segment 4:

-- Q25. What is the genre-wise running total and moving average of the average movie duration? 
-- (Note: You need to show the output table in the question.) 

mysql> SELECT
    -> g.genre,
    -> ROUND(AVG(m.duration), 2) AS avg_duration
    -> FROM genre g
    -> INNER JOIN movie m ON m.id = g.movie_id
    -> GROUP BY g.genre
    -> ORDER BY avg_duration DESC;
+-----------+--------------+
| genre     | avg_duration |
+-----------+--------------+
| Action    |       112.88 |
| Romance   |       109.53 |
| Crime     |       107.05 |
| Drama     |       106.77 |
| Fantasy   |       105.14 |
| Comedy    |       102.62 |
| Adventure |       101.87 |
| Mystery   |       101.80 |
| Thriller  |       101.58 |
| Family    |       100.97 |
| Others    |       100.16 |
| Sci-Fi    |        97.94 |
| Horror    |        92.72 |
+-----------+--------------+
13 rows in set (0.04 sec)


-- Q26. Which are the five highest-grossing movies of each year that belong to the top three genres? 
-- (Note: The top 3 genres would have the most number of movies.)


mysql> SELECT *
    -> FROM (
    ->     SELECT 
    ->         g.genre,
    ->         m.year, 
    ->         m.title, 
    ->         CAST(REPLACE(REPLACE(REPLACE(m.worlwide_gross_income, 'INR', ''), '$', ''), ',', '') AS UNSIGNED) AS cleaned_income,
    ->         RANK() OVER (
    ->             PARTITION BY g.genre, m.year 
    ->             ORDER BY CAST(REPLACE(REPLACE(REPLACE(m.worlwide_gross_income, 'INR', ''), '$', ''), ',', '') AS UNSIGNED) DESC
    ->         ) AS rank_gross_income,
    ->         movie_counts.movie_count,
    ->         DENSE_RANK() OVER (PARTITION BY m.year ORDER BY movie_counts.movie_count DESC) AS rank_movie_count
    ->     FROM genre g
    ->     INNER JOIN movie m ON m.id = g.movie_id AND m.worlwide_gross_income IS NOT NULL
    ->     INNER JOIN (
    ->         SELECT 
    ->             g.genre,
    ->             m.year,
    ->             COUNT(*) AS movie_count
    ->         FROM genre g
    ->         INNER JOIN movie m ON m.id = g.movie_id
    ->         GROUP BY g.genre, m.year
    ->     ) AS movie_counts
    ->     ON g.genre = movie_counts.genre AND m.year = movie_counts.year
    -> ) t1
    -> WHERE t1.rank_movie_count <= 3 AND rank_gross_income <= 5
    -> ORDER BY t1.year, t1.rank_movie_count, t1.rank_gross_income;
+----------+------+---------------------------------------+----------------+-------------------+-------------+------------------+
| genre    | year | title                                 | cleaned_income | rank_gross_income | movie_count | rank_movie_count |
+----------+------+---------------------------------------+----------------+-------------------+-------------+------------------+
| Drama    | 2017 | Zhan lang II                          |      870325439 |                 1 |        1664 |                1 |
| Drama    | 2017 | Logan                                 |      619021436 |                 2 |        1664 |                1 |
| Drama    | 2017 | Shatamanam Bhavati                    |      530500000 |                 3 |        1664 |                1 |
| Drama    | 2017 | Dunkirk                               |      526940665 |                 4 |        1664 |                1 |
| Drama    | 2017 | War for the Planet of the Apes        |      490719763 |                 5 |        1664 |                1 |
| Comedy   | 2017 | Despicable Me 3                       |     1034799409 |                 1 |         908 |                2 |
| Comedy   | 2017 | Jumanji: Welcome to the Jungle        |      962102237 |                 2 |         908 |                2 |
| Comedy   | 2017 | Guardians of the Galaxy Vol. 2        |      863756051 |                 3 |         908 |                2 |
| Comedy   | 2017 | Thor: Ragnarok                        |      853977126 |                 4 |         908 |                2 |
| Comedy   | 2017 | Sing                                  |      634151679 |                 5 |         908 |                2 |
| Thriller | 2017 | The Fate of the Furious               |     1236005118 |                 1 |         571 |                3 |
| Thriller | 2017 | Zhan lang II                          |      870325439 |                 2 |         571 |                3 |
| Thriller | 2017 | xXx: Return of Xander Cage            |      346118277 |                 3 |         571 |                3 |
| Thriller | 2017 | Annabelle: Creation                   |      306515884 |                 4 |         571 |                3 |
| Thriller | 2017 | Split                                 |      278454358 |                 5 |         571 |                3 |
| Drama    | 2018 | Bohemian Rhapsody                     |      903655259 |                 1 |        1543 |                1 |
| Drama    | 2018 | Hong hai xing dong                    |      579220560 |                 2 |        1543 |                1 |
| Drama    | 2018 | Wo bu shi yao shen                    |      451183391 |                 3 |        1543 |                1 |
| Drama    | 2018 | A Star Is Born                        |      434888866 |                 4 |        1543 |                1 |
| Drama    | 2018 | Fifty Shades Freed                    |      371985018 |                 5 |        1543 |                1 |
| Comedy   | 2018 | Deadpool 2                            |      785046920 |                 1 |         930 |                2 |
| Comedy   | 2018 | Ant-Man and the Wasp                  |      622674139 |                 2 |         930 |                2 |
| Comedy   | 2018 | Tang ren jie tan an 2                 |      544061916 |                 3 |         930 |                2 |
| Comedy   | 2018 | Ralph Breaks the Internet             |      529323962 |                 4 |         930 |                2 |
| Comedy   | 2018 | Hotel Transylvania 3: Summer Vacation |      528583774 |                 5 |         930 |                2 |
| Thriller | 2018 | The Villain                           |     1300000000 |                 1 |         515 |                3 |
| Thriller | 2018 | Venom                                 |      856085151 |                 2 |         515 |                3 |
| Thriller | 2018 | Mission: Impossible - Fallout         |      791115104 |                 3 |         515 |                3 |
| Thriller | 2018 | Hong hai xing dong                    |      579220560 |                 4 |         515 |                3 |
| Thriller | 2018 | Fifty Shades Freed                    |      371985018 |                 5 |         515 |                3 |
| Drama    | 2019 | Avengers: Endgame                     |     2797800564 |                 1 |        1078 |                1 |
| Drama    | 2019 | The Lion King                         |     1655156910 |                 2 |        1078 |                1 |
| Drama    | 2019 | Joker                                 |      995064593 |                 3 |        1078 |                1 |
| Drama    | 2019 | Liu lang di qiu                       |      699760773 |                 4 |        1078 |                1 |
| Drama    | 2019 | It Chapter Two                        |      463326885 |                 5 |        1078 |                1 |
| Comedy   | 2019 | Toy Story 4                           |     1073168585 |                 1 |         574 |                2 |
| Comedy   | 2019 | Pokémon Detective Pikachu             |      431705346 |                 2 |         574 |                2 |
| Comedy   | 2019 | The Secret Life of Pets 2             |      429434163 |                 3 |         574 |                2 |
| Comedy   | 2019 | Once Upon a Time... in Hollywood      |      371207970 |                 4 |         574 |                2 |
| Comedy   | 2019 | Shazam!                               |      364571656 |                 5 |         574 |                2 |
| Thriller | 2019 | Joker                                 |      995064593 |                 1 |         398 |                3 |
| Thriller | 2019 | Ne Zha zhi mo tong jiang shi          |      700547754 |                 2 |         398 |                3 |
| Thriller | 2019 | John Wick: Chapter 3 - Parabellum     |      326667460 |                 3 |         398 |                3 |
| Thriller | 2019 | Us                                    |      255105930 |                 4 |         398 |                3 |
| Thriller | 2019 | Glass                                 |      246985576 |                 5 |         398 |                3 |
+----------+------+---------------------------------------+----------------+-------------------+-------------+------------------+
45 rows in set (0.09 sec)


-- Finally, let’s find out the names of the top two production houses that have produced the highest number of hits among multilingual movies.
-- Q27.  Which are the top two production houses that have produced the highest number of hits (median rating >= 8) among multilingual movies?

mysql> SELECT
    -> m.production_company,
    -> COUNT(DISTINCT m.id) AS movie_count
    -> FROM movie m
    -> INNER JOIN ratings r ON r.movie_id = m.id AND r.median_rating >= 8
    -> WHERE m.production_company IS NOT NULL AND m.languages LIKE '%,%'
    -> GROUP BY m.production_company
    -> ORDER BY movie_count DESC
    -> LIMIT 2;
+-----------------------+-------------+
| production_company    | movie_count |
+-----------------------+-------------+
| Star Cinema           |           7 |
| Twentieth Century Fox |           4 |
+-----------------------+-------------+
2 rows in set (0.03 sec)


-- Q28. Who are the top 3 actresses based on number of Super Hit movies (average rating >8) in drama genre?
mysql> SELECT *
    -> FROM (
    ->     SELECT
    ->         n.name,
    ->         COUNT(DISTINCT rm.movie_id) AS movie_count,
    ->         RANK() OVER (ORDER BY COUNT(DISTINCT rm.movie_id) DESC) AS rank_value
    ->     FROM names n
    ->     INNER JOIN role_mapping rm ON rm.name_id = n.id
    ->     INNER JOIN genre g ON g.movie_id = rm.movie_id AND g.genre = 'Drama'
    ->     INNER JOIN ratings r ON r.movie_id = rm.movie_id AND r.avg_rating > 8
    ->     WHERE rm.category = 'actress'
    ->     GROUP BY n.name
    -> ) AS t1
    -> WHERE t1.rank_value <= 3
    -> ORDER BY t1.rank_value;
+---------------------+-------------+------------+
| name                | movie_count | rank_value |
+---------------------+-------------+------------+
| Amanda Lawrence     |           2 |          1 |
| Denise Gough        |           2 |          1 |
| Parvathy Thiruvothu |           2 |          1 |
| Susan Brown         |           2 |          1 |
+---------------------+-------------+------------+
4 rows in set (0.03 sec)


/* Q29. Get the following details for top 9 directors (based on number of movies)
Director id
Name
Number of movies
Average inter movie duration in days
Average movie ratings
Total votes
Min rating
Max rating
total movie durations */


mysql> SELECT
    -> director_id, name,
    -> COUNT(movie_id) AS number_of_movies,
    -> ROUND(AVG(days_between_movies), 0) AS average_inter_movie_duration_in_days,
    -> ROUND(AVG(avg_rating), 2) AS average_movie_ratings,
    -> SUM(total_votes) AS total_votes,
    -> MIN(total_votes) AS min_total_votes,
    -> MAX(total_votes) AS max_total_votes,
    -> SUM(duration) AS total_movie_durations
    -> FROM (
    -> SELECT
    -> n.id AS director_id, n.name,
    -> m.id AS movie_id, m.title, m.date_published,
    -> (m.date_published - LAG(m.date_published) OVER (PARTITION BY n.id ORDER BY m.date_published)) AS days_between_movies,
    -> m.duration,
    -> r.avg_rating, r.total_votes
    -> FROM names n
    -> INNER JOIN director_mapping dm ON dm.name_id = n.id
    -> INNER JOIN movie m ON m.id = dm.movie_id
    -> INNER JOIN ratings r ON r.movie_id = m.id
    -> ) t1
    -> GROUP BY director_id, name;




