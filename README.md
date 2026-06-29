# Zomato Bangalore Restaurants - SQL Data Analysis

## 📌 Project Overview
Performed exploratory data analysis on Zomato Bangalore dataset containing 51,000+ restaurants using SQLite. Extracted business insights on location-wise pricing, cuisine popularity, and rating trends using complex SQL queries.

## 🛠️ Tech Stack
- **Database**: SQLite
- **Tool**: DB Browser for SQLite
- **Concepts Used**: GROUP BY, Aggregate Functions, Window Functions, Data Cleaning with CAST & REPLACE

## 📊 Dataset
- **Source**: Zomato Bangalore Restaurants Dataset
- **Size**: 51,000+ rows
- **Key Columns**: `name`, `location`, `rate`, `approx_cost(for two people)`, `online_order`, `votes`, `cuisines`

## 📸 Query Results

### 1. Most Expensive Areas
![Query 1](Screenshot%20(46).png)

### 2. Online Order Impact  
![Query 2](Screenshot%20(47).png)

### 3. Top Cuisines
![Query 3](Screenshot%20(48).png)

### 4. Best Restaurant Per Location
![Query 4](Screenshot%20(49).png)

### 5. Costly But Low Rating
![Query 5](Screenshot%20(50).png)

## 🔍 Key Business Questions Solved

### 1. Which are the most expensive areas in Bangalore?
**Query**: Used `AVG`, `GROUP BY`, `REPLACE` to handle comma-separated cost values.
```sql
SELECT 
    location, 
    COUNT(*) as total_restaurants,
    ROUND(AVG(CAST(REPLACE(`approx_cost(for two people)`, ',', '') AS INTEGER)), 0) as avg_cost_for_two
FROM zomato
WHERE `approx_cost(for two people)` NOT IN ('0', '') AND location!= ''
GROUP BY location 
HAVING COUNT(*) > 5
ORDER BY avg_cost_for_two DESC LIMIT 10;
Insight: Sankey Road (₹2506), Race Course Road (₹1309), and Lavelle Road (₹1308) are the costliest locations.


### 2. Does online ordering impact restaurant ratings?
Query: Compared average ratings for restaurants with/without online delivery.
```sql
SELECT 
    online_order,
    COUNT(*) as restaurant_count,
    ROUND(AVG(CAST(rate AS FLOAT)), 2) as avg_rating,
    ROUND(AVG(votes), 0) as avg_votes
FROM zomato
WHERE rate NOT IN ('NEW', '', '-') AND rate IS NOT NULL
GROUP BY online_order;
Insight: Restaurants with online ordering have higher average ratings and more customer votes.

3. Which cuisines have the highest ratings?
Query: Filtered cuisines with 100+ votes and 10+ restaurants.
SELECT 
    cuisines,
    COUNT(*) as total_count,
    ROUND(AVG(CAST(rate AS FLOAT)), 2) as avg_rating
FROM zomato
WHERE rate NOT IN ('NEW', '', '-') AND votes > 100
GROUP BY cuisines
HAVING COUNT(*) > 10
ORDER BY avg_rating DESC LIMIT 10;
Insight: Identified top-performing cuisines based on customer ratings and popularity.

4. Top-rated restaurant in each location
Query: Used ROW_NUMBER() Window Function to rank restaurants per area.
SELECT * FROM (
    SELECT 
        name, location, CAST(rate AS FLOAT) as rating, votes,
        ROW_NUMBER() OVER(PARTITION BY location ORDER BY CAST(rate AS FLOAT) DESC, votes DESC) as rank
    FROM zomato
    WHERE rate NOT IN ('NEW', '', '-') AND location!= ''
) WHERE rank = 1
ORDER BY rating DESC LIMIT 15;
Insight: Found the #1 restaurant for each major location in Bangalore.

5. Costly restaurants with low ratings
Query: Found overpriced restaurants charging >₹1000 but rated <3.5.
SELECT 
    name, location, `approx_cost(for two people)` as cost, rate, votes
FROM zomato
WHERE CAST(REPLACE(`approx_cost(for two people)`, ',', '') AS INTEGER) > 1000 
    AND CAST(rate AS FLOAT) < 3.5 AND votes > 50
ORDER BY CAST(REPLACE(`approx_cost(for two people)`, ',', '') AS INTEGER) DESC LIMIT 10;
Insight: Identified restaurants with poor value-for-money to avoid.



## 📈 Key Learnings
1. Data Cleaning: Handled commas in numeric fields using `REPLACE` and type conversion with `CAST`.
2. Window Functions: Used `ROW_NUMBER() OVER(PARTITION BY)` for advanced ranking.
3. Business Insights: Converted raw data into actionable insights on pricing and customer preferences.

## 🚀 How to Run
1. Download `restaurants.db` file.
2. Open with DB Browser for SQLite.
3. Run the queries from the `Execute SQL` tab.

 


