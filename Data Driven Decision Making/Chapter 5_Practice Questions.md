# **MOVIENOW SQL WORKBOOK**

## Chapter 1

## Exploring the Table `renting`

**Goal:** The table `renting` includes all records of movie rentals. Each record has a unique `renting_id`, along with `customer_id`, `movie_id`, optional `rating`, and `date_renting`.

### Select All Columns

```sql
-- Select all columns from renting
SELECT *  
FROM renting;
```

### Select Columns Needed for Average Rating per Movie

```sql
-- Select only columns needed to compute the average rating per movie
SELECT 
    rating,
    movie_id
FROM renting;
```

## Working with Dates

**Goal:** Analyze rentals across specific periods for monthly or annual trends.
Dates are in the format `'YYYY-MM-DD'`.

### Movies Rented on a Specific Date

```sql
-- Select all movies rented on October 9th, 2018
SELECT *
FROM renting
WHERE date_renting = '2018-10-09';
```

### Movies Rented Between Two Dates

```sql
-- Select all records of movie rentals between April 1, 2018 and August 31, 2018
SELECT *
FROM renting
WHERE date_renting BETWEEN '2018-04-01' AND '2018-08-31';
```

### Order by Most Recent Rentals

```sql
-- Return rentals from April–August 2018, ordered by recency (newest first)
SELECT *
FROM renting
WHERE date_renting BETWEEN '2018-04-01' AND '2018-08-31'
ORDER BY date_renting DESC;
```

## Selecting Movies

**Goal:** Query information from the `movies` table to analyze available content and prices.

### Movies That Are Not Dramas

```sql
-- Select all movies which are not dramas
SELECT *
FROM movies
WHERE genre <> 'Drama';
```

### Specific Movie Titles

```sql
-- Select the movies 'Showtime', 'Love Actually', and 'The Fighter'
SELECT *
FROM movies
WHERE title IN ('Showtime', 'Love Actually', 'The Fighter');
```

### Order Movies by Renting Price

```sql
-- Order the movies by increasing renting price
SELECT *
FROM movies
ORDER BY renting_price;
```

## Selecting from Renting (Ratings Only)

**Goal:** Focus on rentals where a rating was given in 2018.

```sql
-- Select all movie rentals from 2018 that have a rating
SELECT *
FROM renting
WHERE date_renting BETWEEN '2018-01-01' AND '2018-12-31'
  AND rating IS NOT NULL;
```

## Summarizing Customer Information

**Goal:** Analyze customer demographics and distribution by country and birth year.


### Customers Born in the 1980s

```sql
-- Count the number of customers born in the 1980s
SELECT COUNT(*) AS total_customers_80s
FROM customers
WHERE date_of_birth BETWEEN '1980-01-01' AND '1989-12-31';
```

### Customers from Germany

```sql
-- Count the number of customers from Germany
SELECT COUNT(*) AS customers_germany
FROM customers
WHERE country = 'Germany';
```

---

### Number of Countries with Customers

```sql
-- Count the number of distinct countries where MovieNow has customers
SELECT COUNT(DISTINCT country) AS number_of_countries
FROM customers
WHERE customer_id IS NOT NULL;
```

## Ratings of Movie 25

**Goal:** Compute summary statistics (min, max, average, count) for ratings of movie with `movie_id = 25`.

```sql
-- Calculate summary statistics for ratings of movie 25
SELECT 
    MIN(rating) AS min_rating,
    MAX(rating) AS max_rating,
    AVG(rating) AS avg_rating,
    COUNT(rating) AS number_ratings
FROM renting
WHERE movie_id = 25;
```

## Examining Annual Rentals

**Goal:** Provide a performance report since the beginning of 2019 — total rentals, number of ratings, and average rating.

### Rentals Since January 1st, 2019

```sql
-- Select all records of movie rentals since January 1st, 2019
SELECT *
FROM renting
WHERE date_renting >= '2019-01-01';
```

### Count Rentals and Average Rating Since 2019

```sql
-- Count total number of rentals and calculate average rating since 2019
SELECT 
    COUNT(renting_id) AS number_renting,
    AVG(rating) AS average_rating
FROM renting
WHERE date_renting >= '2019-01-01';
```

### Include Total Ratings Count Since 2019

```sql
-- Add count of ratings along with rentals and average rating
SELECT 
    COUNT(*) AS number_renting,
    AVG(rating) AS average_rating,
    COUNT(rating) AS number_ratings
FROM renting
WHERE date_renting >= '2019-01-01';
```

## First Account for Each Country

**Goal:** Find the earliest account creation date (`date_account_start`) for each country.

```sql
-- For each country, report the earliest date when an account was created
SELECT 
    country,
    MIN(date_account_start) AS first_account
FROM customers
GROUP BY country
ORDER BY first_account;
```

---
# Chapter 2

### **1. Average Movie Ratings**

**Goal:**
For each movie, report:

* Average rating
* Number of ratings
* Number of rentals

```sql
-- Average rating per movie
SELECT 
    movie_id, 
    AVG(rating) AS avg_rating,           -- Average rating per movie
    COUNT(rating) AS number_rating,      -- Number of ratings received
    COUNT(*) AS number_renting           -- Total rentals for the movie
FROM renting
GROUP BY movie_id
ORDER BY avg_rating DESC;                -- Order by average rating (highest first)
```

### **2. Average Rating per Customer**

**Goal:**
For each customer:

* Average rating given
* Number of ratings
* Number of rentals

Only include customers with **more than 7 rentals**.

```sql
SELECT 
    customer_id,                         -- Customer ID
    AVG(rating) AS avg_rating,           -- Average rating by customer
    COUNT(rating) AS number_rating,      -- Number of ratings by customer
    COUNT(*) AS number_renting           -- Total rentals by customer
FROM renting
GROUP BY customer_id
HAVING COUNT(*) > 7                      -- Include only active customers
ORDER BY AVG(rating) ASC;                -- Order by lowest to highest rating
```

---

### **3. Joining Renting and Customers**

**Goal:**
Combine rental data with customer information.

```sql
-- Join renting with customers
SELECT *
FROM renting AS r
LEFT JOIN customers AS c
    ON r.customer_id = c.customer_id;
```

**Filter:** Only customers from **Belgium**.

```sql
SELECT *
FROM renting AS r
LEFT JOIN customers AS c
    ON r.customer_id = c.customer_id
WHERE c.country = 'Belgium';
```

**Average rating from Belgian customers:**

```sql
SELECT 
    AVG(r.rating) AS avg_rating_belgium
FROM renting AS r
LEFT JOIN customers AS c
    ON r.customer_id = c.customer_id
WHERE c.country = 'Belgium';
```

### **4. Aggregating Revenue, Rentals & Active Customers (KPIs)**

**Goal:**
Calculate MovieNow’s KPIs for **2018**:

* Total revenue
* Total rentals
* Active customers

```sql
-- Join renting with movies to include rental price
SELECT *
FROM renting AS r
LEFT JOIN movies AS m
    ON r.movie_id = m.movie_id;
```

**Calculate KPIs for 2018:**

```sql
SELECT 
    SUM(m.renting_price) AS total_revenue,           -- Total revenue
    COUNT(*) AS total_rentals,                       -- Total rentals
    COUNT(DISTINCT r.customer_id) AS active_customers -- Unique active customers
FROM renting AS r
LEFT JOIN movies AS m
    ON r.movie_id = m.movie_id
WHERE date_renting BETWEEN '2018-01-01' AND '2018-12-31';
```

### **5. Movies and Actors**

**Goal:**
List each actor and the movies they appear in.

---

```sql
SELECT 
    a.name AS actor_name, 
    m.title AS movie_title
FROM actsin AS ai
LEFT JOIN movies AS m
    ON m.movie_id = ai.movie_id
LEFT JOIN actors AS a
    ON a.actor_id = ai.actor_id;
```

### 💵 **6. Income from Movies**

**Goal:**
Calculate total income per movie and order by revenue.

```sql
-- Step 1: Get title and price per rental
SELECT 
    m.title,  
    m.renting_price
FROM renting AS r
LEFT JOIN movies AS m
    ON r.movie_id = m.movie_id;
```

**Step 2: Aggregate total income:**

```sql
SELECT 
    rm.title, 
    SUM(rm.renting_price) AS income_movie
FROM (
    SELECT 
        m.title, 
        m.renting_price
    FROM renting AS r
    LEFT JOIN movies AS m
        ON r.movie_id = m.movie_id
) AS rm
GROUP BY rm.title
ORDER BY income_movie DESC;
```

### **7. Age of Actors from the USA**

**Goal:**
Find the oldest and youngest US actors/actresses by gender.


```sql
SELECT 
    a.gender, 
    MIN(a.year_of_birth) AS oldest_year,  -- Oldest actor birth year
    MAX(a.year_of_birth) AS youngest_year -- Youngest actor birth year
FROM (
    SELECT * 
    FROM actors
    WHERE nationality = 'USA'
) AS a
GROUP BY a.gender;
```

### **8. Favorite Movies (Customers Born in the 1970s)**

**Goal:**
Find top-rated movies for customers born in the 1970s.

```sql
-- Join renting with customers and movies
SELECT *
FROM renting AS r
LEFT JOIN customers AS c
    ON c.customer_id = r.customer_id
LEFT JOIN movies AS m
    ON m.movie_id = r.movie_id
WHERE c.date_of_birth BETWEEN '1970-01-01' AND '1979-12-31';
```

**Aggregate and rank:**

```sql
SELECT 
    m.title, 
    COUNT(*) AS total_views, 
    AVG(r.rating) AS avg_rating
FROM renting AS r
LEFT JOIN customers AS c
    ON c.customer_id = r.customer_id
LEFT JOIN movies AS m
    ON m.movie_id = r.movie_id
WHERE c.date_of_birth BETWEEN '1970-01-01' AND '1979-12-31'
GROUP BY m.title
HAVING COUNT(*) > 1                 -- Remove movies with only one rental
ORDER BY AVG(r.rating) DESC;        -- Highest-rated first
```

### **9. Favorite Actors (Spain)**

**Goal:**
Find favorite actors by gender for Spanish customers.


```sql
-- Combine renting, customers, actsin, and actors
SELECT *
FROM renting AS r
LEFT JOIN customers AS c
    ON r.customer_id = c.customer_id
LEFT JOIN actsin AS ai
    ON r.movie_id = ai.movie_id
LEFT JOIN actors AS a
    ON ai.actor_id = a.actor_id;
```

**Aggregate by actor & gender:**

```sql
SELECT 
    a.name AS actor_name,  
    c.gender, 
    COUNT(*) AS number_views, 
    AVG(r.rating) AS avg_rating
FROM renting AS r
LEFT JOIN customers AS c
    ON r.customer_id = c.customer_id
LEFT JOIN actsin AS ai
    ON r.movie_id = ai.movie_id
LEFT JOIN actors AS a
    ON ai.actor_id = a.actor_id
WHERE c.country = 'Spain'           -- Limit to Spanish customers
GROUP BY a.name, c.gender
HAVING AVG(r.rating) IS NOT NULL 
   AND COUNT(*) > 5                 -- At least 5 rentals
ORDER BY avg_rating DESC, number_views DESC;
```

### **10. KPIs per Country (Since 2019)**

**Goal:**
For each country, calculate:

* Total movie rentals
* Average rating
* Total revenue

```sql
SELECT 
    c.country, 
    COUNT(*) AS number_renting,        -- Total rentals
    AVG(r.rating) AS average_rating,   -- Avg rating
    SUM(m.renting_price) AS revenue    -- Total revenue
FROM renting AS r
LEFT JOIN customers AS c
    ON c.customer_id = r.customer_id
LEFT JOIN movies AS m
    ON r.movie_id = m.movie_id
WHERE date_renting >= '2019-01-01'
GROUP BY c.country;
```

---

# CHAPTER 3 

##  1. **Often Rented Movies**

**Goal:**
List movies that are frequently watched (more than 5 rentals).
These will be used for targeted advertising.

---

```sql
-- Step 1: Select all movie IDs with more than 5 views
SELECT 
    movie_id
FROM renting
GROUP BY movie_id
HAVING COUNT(*) > 5;
```

```sql
-- Step 2: Retrieve all movie details for movies with more than 5 views
SELECT *
FROM movies
WHERE movie_id IN (
    SELECT movie_id
    FROM renting
    GROUP BY movie_id
    HAVING COUNT(*) > 5
);
```

## 2. **Frequent Customers**

**Goal:**
Identify customers who rented more than 10 movies.

---

```sql
SELECT *
FROM customers
WHERE customer_id IN (
    SELECT customer_id
    FROM renting
    GROUP BY customer_id
    HAVING COUNT(*) > 10
);
```

## 3. **Movies with Rating Above Average**

**Goal:**
Find movies with an average rating higher than the **overall average**.

---

```sql
-- Step 1: Calculate the total average rating
SELECT AVG(rating)
FROM renting;
```

```sql
-- Step 2: Find movie IDs with above-average ratings
SELECT 
    movie_id,
    AVG(rating) AS avg_rating
FROM renting
GROUP BY movie_id
HAVING AVG(rating) > (
    SELECT AVG(rating)
    FROM renting
);
```

```sql
-- Step 3: Return only movie titles above the overall average
SELECT 
    title
FROM movies
WHERE movie_id IN (
    SELECT movie_id
    FROM renting
    GROUP BY movie_id
    HAVING AVG(rating) > (
        SELECT AVG(rating)
        FROM renting
    )
);
```

## 4. **Analyzing Customer Behavior**

**Goal:**
Find customers who rented **fewer than 5 movies** using a **correlated subquery**.

---

```sql
-- Step 1: Count movie rentals of a specific customer (e.g., ID = 45)
SELECT COUNT(renting_id)
FROM renting AS r
WHERE customer_id = 45;
```

```sql
-- Step 2: Select all customers with fewer than 5 rentals
SELECT *
FROM customers AS c
WHERE (
    SELECT COUNT(*)
    FROM renting AS r
    WHERE r.customer_id = c.customer_id
) < 5;
```

## 5. **Customers Who Gave Low Ratings**

**Goal:**
Find customers whose **lowest rating** is less than 4.

---

```sql
-- Step 1: Check minimum rating for a sample customer (ID = 7)
SELECT MIN(rating)
FROM renting
WHERE customer_id = 7;
```

```sql
-- Step 2: Retrieve all customers whose minimum rating < 4
SELECT *
FROM customers AS c
WHERE 4 > (
    SELECT MIN(rating)
    FROM renting AS r
    WHERE r.customer_id = c.customer_id
);
```

## 6. **Movies and Ratings (Correlated Queries)**

**Goal:**
Report movies that either:

* Have **more than 5 ratings**, or
* Have an **average rating higher than 8**

---

```sql
-- Movies with more than 5 ratings
SELECT *
FROM movies AS m
WHERE 5 < (
    SELECT COUNT(rating)
    FROM renting AS r
    WHERE r.movie_id = m.movie_id
);
```

```sql
-- Movies with an average rating higher than 8
SELECT *
FROM movies AS m
WHERE 8 < (
    SELECT AVG(rating)
    FROM renting AS r
    WHERE r.movie_id = m.movie_id
);
```

## 7. **Customers with At Least One Rating**

**Goal:**
Find customers who have submitted at least one movie rating.

---

```sql
-- Example: Rentals from customer ID 115 (with non-null ratings)
SELECT *
FROM renting
WHERE rating IS NOT NULL
  AND customer_id = 115;
```

```sql
-- List of all customers with at least one rating
SELECT *
FROM customers AS c
WHERE EXISTS (
    SELECT *
    FROM renting AS r
    WHERE r.rating IS NOT NULL
      AND r.customer_id = c.customer_id
);
```

## 8. **Actors in Comedies**

**Goal:**
Analyze actor diversity in **Comedy** movies.

---

```sql
-- Step 1: Actors appearing in Comedy movies
SELECT *
FROM actsin AS ai
LEFT JOIN movies AS m
    ON ai.movie_id = m.movie_id
WHERE m.genre = 'Comedy';
```

```sql
-- Step 2: Select actor with ID = 1 from Comedy movies
SELECT *
FROM actsin AS ai
LEFT JOIN movies AS m
    ON ai.movie_id = m.movie_id
WHERE m.genre = 'Comedy'
  AND ai.actor_id = 1;
```

```sql
-- Step 3: List all actors who play in a Comedy
SELECT *
FROM actors AS a
WHERE EXISTS (
    SELECT *
    FROM actsin AS ai
    LEFT JOIN movies AS m
        ON m.movie_id = ai.movie_id
    WHERE m.genre = 'Comedy'
      AND ai.actor_id = a.actor_id
);
```

```sql
-- Step 4: Count number of Comedy actors by nationality
SELECT 
    a.nationality, 
    COUNT(*) AS number_of_actors
FROM actors AS a
WHERE EXISTS (
    SELECT ai.actor_id
    FROM actsin AS ai
    LEFT JOIN movies AS m
        ON m.movie_id = ai.movie_id
    WHERE m.genre = 'Comedy'
      AND ai.actor_id = a.actor_id
)
GROUP BY a.nationality;
```

## 9. **Young Actors Not from the USA**

**Goal:**
Use `UNION` and `INTERSECT` to analyze actor demographics.

---

```sql
-- Actors not from the USA
SELECT 
    name, nationality, year_of_birth
FROM actors
WHERE nationality <> 'USA';
```

```sql
-- Actors born after 1990
SELECT 
    name, nationality, year_of_birth
FROM actors
WHERE year_of_birth > 1990;
```

```sql
-- Combine both groups: not from USA OR born after 1990
SELECT 
    name, nationality, year_of_birth
FROM actors
WHERE nationality <> 'USA'
UNION
SELECT 
    name, nationality, year_of_birth
FROM actors
WHERE year_of_birth > 1990;
```

```sql
-- Intersection: not from USA AND born after 1990
SELECT 
    name, nationality, year_of_birth
FROM actors
WHERE nationality <> 'USA'
INTERSECT
SELECT 
    name, nationality, year_of_birth
FROM actors
WHERE year_of_birth > 1990;
```

## 10. **Dramas with High Ratings**

**Goal:**
List all **Drama** movies with an **average rating > 9**.


```sql
-- Step 1: Select IDs of all Dramas
SELECT movie_id
FROM movies
WHERE genre = 'Drama';
```

```sql
-- Step 2: Select IDs of movies with average rating > 9
SELECT movie_id
FROM renting
GROUP BY movie_id
HAVING AVG(rating) > 9;
```

```sql
-- Step 3: Intersection — Dramas with avg rating > 9
SELECT movie_id
FROM movies
WHERE genre = 'Drama'
INTERSECT
SELECT movie_id
FROM renting
GROUP BY movie_id
HAVING AVG(rating) > 9;
```

```sql
-- Step 4: Full details of high-rated Dramas
SELECT *
FROM movies
WHERE movie_id IN (
    SELECT movie_id
    FROM movies
    WHERE genre = 'Drama'
    INTERSECT
    SELECT movie_id
    FROM renting
    GROUP BY movie_id
    HAVING AVG(rating) > 9
);
```

---

## Chapter 4 — Advanced Aggregations & OLAP Analysis


### Groups of Customers

**Goal:** Use the `CUBE` operator to create a pivot-style summary table of customer counts by gender and country.

```sql
SELECT 
    gender,        -- Gender of customers
    country,       -- Country of customers
    COUNT(*) AS num_customers
FROM customers
GROUP BY CUBE (gender, country)
ORDER BY country;
```

### Categories of Movies

**Goal:** List number of movies for different genres and release years at all aggregation levels.

```sql
SELECT 
    year_of_release, 
    genre, 
    COUNT(*) AS num_movies
FROM movies
GROUP BY CUBE (year_of_release, genre)
ORDER BY year_of_release;
```

### Analyzing Average Ratings

**Goal:** Compare average movie ratings across customer countries and genres.

**Join movies, renting, and customers:**

```sql
SELECT *
FROM renting AS r
LEFT JOIN movies AS m
    ON m.movie_id = r.movie_id
LEFT JOIN customers AS c
    ON r.customer_id = c.customer_id;
```

**Average rating per country:**

```sql
SELECT 
    c.country, 
    AVG(r.rating) AS avg_rating
FROM renting AS r
LEFT JOIN movies AS m
    ON m.movie_id = r.movie_id
LEFT JOIN customers AS c
    ON r.customer_id = c.customer_id
GROUP BY c.country;
```

**Average rating for all aggregation levels of country and genre:**

```sql
SELECT 
    c.country, 
    m.genre, 
    AVG(r.rating) AS avg_rating
FROM renting AS r
LEFT JOIN movies AS m
    ON m.movie_id = r.movie_id
LEFT JOIN customers AS c
    ON r.customer_id = c.customer_id
GROUP BY CUBE (c.country, m.genre);
```

### Number of Customers

**Goal:** Generate an overview of total customers and breakdown by country and gender.

```sql
SELECT 
    country,
    gender,
    COUNT(*) AS num_customers
FROM customers
GROUP BY ROLLUP (country, gender)
ORDER BY country, gender;
```

### Analyzing Preferences of Genres Across Countries

**Goal:** Evaluate customer preferences for genres by country based on average ratings and number of rentals.

**Join data:**

```sql
SELECT *
FROM renting AS r
LEFT JOIN movies AS m
    ON m.movie_id = r.movie_id
LEFT JOIN customers AS c
    ON r.customer_id = c.customer_id;
```

**Average rating and number of ratings per country and genre:**

```sql
SELECT 
    c.country,
    m.genre,
    AVG(r.rating) AS avg_rating,
    COUNT(*) AS num_rentals
FROM renting AS r
LEFT JOIN movies AS m
    ON m.movie_id = r.movie_id
LEFT JOIN customers AS c
    ON r.customer_id = c.customer_id
GROUP BY c.country, m.genre
ORDER BY c.country, m.genre;
```

**Add aggregation levels using ROLLUP:**

```sql
SELECT 
    c.country, 
    m.genre, 
    AVG(r.rating) AS avg_rating, 
    COUNT(*) AS num_ratings
FROM renting AS r
LEFT JOIN movies AS m
    ON m.movie_id = r.movie_id
LEFT JOIN customers AS c
    ON r.customer_id = c.customer_id
GROUP BY ROLLUP (c.country, m.genre)
ORDER BY c.country, m.genre;
```

### Exploring Nationality and Gender of Actors

**Goal:** Measure diversity of actors by nationality and gender using `GROUPING SETS`.

```sql
SELECT 
    nationality, 
    gender, 
    COUNT(*) AS num_actors
FROM actors
GROUP BY GROUPING_SETS (
    (nationality),
    (gender),
    ()
);
```

### Exploring Rating by Country and Gender

**Goal:** Investigate average movie rating by customer country and gender.

**Join renting with customers:**

```sql
SELECT 
    c.country, 
    c.gender, 
    r.rating
FROM renting AS r
LEFT JOIN customers AS c
    ON r.customer_id = c.customer_id;
```

**Average rating by country and gender:**

```sql
SELECT 
    c.country, 
    c.gender,
    AVG(r.rating) AS avg_rating
FROM renting AS r
LEFT JOIN customers AS c
    ON r.customer_id = c.customer_id
GROUP BY c.country, c.gender
ORDER BY c.country, c.gender;
```

**Same using GROUPING SETS:**

```sql
SELECT 
    c.country, 
    c.gender,
    AVG(r.rating) AS avg_rating
FROM renting AS r
LEFT JOIN customers AS c
    ON r.customer_id = c.customer_id
GROUP BY GROUPING SETS ((c.country, c.gender));
```

**Pivot table version (all aggregation levels):**

```sql
SELECT 
    c.country, 
    c.gender,
    AVG(r.rating) AS avg_rating
FROM renting AS r
LEFT JOIN customers AS c
    ON r.customer_id = c.customer_id
GROUP BY GROUPING SETS (
    (c.country, c.gender),
    (c.country),
    (c.gender),
    ()
);
```

### Customer Preference for Genres

**Goal:** Analyze best-rated genres since 2018.

**Join renting and movies:**

```sql
SELECT *
FROM renting AS r
LEFT JOIN movies AS m
    ON r.movie_id = m.movie_id;
```

**Filter for movies with ≥ 4 ratings since April 2018:**

```sql
SELECT *
FROM renting AS r
LEFT JOIN movies AS m
    ON m.movie_id = r.movie_id
WHERE r.movie_id IN (
        SELECT movie_id
        FROM renting
        GROUP BY movie_id
        HAVING COUNT(rating) >= 4
    )
  AND r.date_renting >= '2018-04-01';
```

**Genre-level aggregation:**

```sql
SELECT 
    m.genre,
    AVG(r.rating) AS avg_rating,
    COUNT(r.rating) AS n_rating,
    COUNT(*) AS n_rentals,
    COUNT(DISTINCT m.movie_id) AS n_movies
FROM renting AS r
LEFT JOIN movies AS m
    ON m.movie_id = r.movie_id
WHERE r.movie_id IN (
        SELECT movie_id
        FROM renting
        GROUP BY movie_id
        HAVING COUNT(rating) >= 3
    )
  AND r.date_renting >= '2018-01-01'
GROUP BY m.genre
ORDER BY avg_rating DESC;
```

### Customer Preference for Actors

**Goal:** Analyze customer ratings and engagement by actor nationality and gender.

**Join renting, actsin, and actors:**

```sql
SELECT *
FROM renting AS r
LEFT JOIN actsin AS ai
    ON ai.movie_id = r.movie_id
LEFT JOIN actors AS a
    ON ai.actor_id = a.actor_id;
```

**Aggregate metrics by nationality and gender:**

```sql
SELECT 
    a.nationality,
    a.gender,
    AVG(r.rating) AS avg_rating,
    COUNT(r.rating) AS n_rating,
    COUNT(*) AS n_rentals,
    COUNT(DISTINCT a.actor_id) AS n_actors
FROM renting AS r
LEFT JOIN actsin AS ai
    ON ai.movie_id = r.movie_id
LEFT JOIN actors AS a
    ON ai.actor_id = a.actor_id
WHERE r.movie_id IN (
        SELECT movie_id
        FROM renting
        GROUP BY movie_id
        HAVING COUNT(rating) >= 4
    )
  AND r.date_renting >= '2018-04-01'
GROUP BY a.nationality, a.gender;
```

**Include all aggregation levels with CUBE:**

```sql
SELECT 
    a.nationality,
    a.gender,
    AVG(r.rating) AS avg_rating,
    COUNT(r.rating) AS n_rating,
    COUNT(*) AS n_rentals,
    COUNT(DISTINCT a.actor_id) AS n_actors
FROM renting AS r
LEFT JOIN actsin AS ai
    ON ai.movie_id = r.movie_id
LEFT JOIN actors AS a
    ON ai.actor_id = a.actor_id
WHERE r.movie_id IN (
        SELECT movie_id
        FROM renting
        GROUP BY movie_id
        HAVING COUNT(rating) >= 4
    )
  AND r.date_renting >= '2018-04-01'
GROUP BY CUBE (a.nationality, a.gender);
```

---






















