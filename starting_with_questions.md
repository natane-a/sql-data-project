Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


## SQL Queries:

```sql
SELECT al.city AS city, 
		SUM((a.unit_price / 1000000)* a.units_sold) AS transaction_revenue
FROM all_sessions al
JOIN analytics a ON al.full_visitor_id = a.full_visitor_id
WHERE ((a.unit_price / 1000000)* a.units_sold) IS NOT NULL 
	   AND city != 'not available in demo dataset'
GROUP BY al.city
ORDER BY transaction_revenue DESC
```
```sql
SELECT al.country AS country, 
		SUM((a.unit_price / 1000000)* a.units_sold) AS transaction_revenue
FROM all_sessions al
JOIN analytics a ON al.full_visitor_id = a.full_visitor_id
WHERE ((a.unit_price / 1000000)* a.units_sold) IS NOT NULL 
GROUP BY al.country
ORDER BY transaction_revenue DESC
```

##  Answer:
The cities with the highest transaction revenue are Mountain View, San Bruno, New York, Chicago, and Sunny Vale.
The countries with the highest transaction revenue are The United States, Czechia, Canada, Hong Kong, and Mexico.


**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:
```sql
WITH distinct_visitor_id AS (
	SELECT DISTINCT(al.full_visitor_id) AS full_visitor_id, al.city, a.units_sold AS units_sold
	FROM all_sessions al
	JOIN analytics a ON al.full_visitor_id = a.full_visitor_id
	WHERE a.units_sold IS NOT NULL AND city != 'not available in demo dataset'
)
SELECT city, AVG(units_sold) AS average_units_sold
FROM distinct_visitor_id
GROUP BY city
ORDER BY average_units_sold DESC;
```
```sql
WITH distinct_visitor_id AS (
	SELECT DISTINCT(al.full_visitor_id) AS full_visitor_id, al.country, a.units_sold AS units_sold
	FROM all_sessions al
	JOIN analytics a ON al.full_visitor_id = a.full_visitor_id
	WHERE a.units_sold IS NOT NULL
)
SELECT country, AVG(units_sold) AS average_units_sold
FROM distinct_visitor_id
GROUP BY country
ORDER BY average_units_sold DESC;
```


## Answer:
The average units sold in each city can be seen using the query above, with the top 3 cities being San Bruno, Seattle and Sunnyvale.
The average units sold in each country can be seen using the query above, with the top 3 being Czechia, United States and Canada.



**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:
```sql
WITH top_categories AS(
	SELECT 	country,
			city,
			v2_product_category AS product_category,
			COUNT(*) AS category_count,
			RANK() OVER (PARTITION BY city, country
			ORDER BY COUNT(*) DESC) AS rank
	FROM all_sessions
	WHERE v2_product_category != '(not set)' AND city != 'not available in demo dataset' 
	GROUP BY city, country, product_category
)
SELECT city, country, product_category, category_count
FROM top_categories
WHERE rank <=5 
ORDER BY category_count DESC, rank
```

## Answer:
Using the query above, I have gathered that mens apparel is the most popular category in cities within The United States, while youtube brand is the most popular in cities outside of The United States.




**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:
```sql
WITH top_products AS(
	SELECT 	country,
			city,
			v2_product_name AS product_name,
			COUNT(*) AS product_count,
			RANK() OVER (PARTITION BY city, country
			ORDER BY COUNT(*) DESC) AS rank
	FROM all_sessions
	WHERE v2_product_name != '(not set)' AND city != 'not available in demo dataset' 
	GROUP BY city, country, product_name
)
SELECT city, country, product_name, product_count
FROM top_products
WHERE rank <=1
ORDER BY product_count DESC, rank
```

## Answer:
The top product for most cities in the United States is the Google Men's White Tee, 
while in other countries it ranges from google tees to youtube merch.




**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

```sql
SELECT 	al.city AS city,
		al.country AS country, 
		SUM((a.unit_price / 1000000)* a.units_sold) AS transaction_revenue
FROM all_sessions al
JOIN analytics a ON al.full_visitor_id = a.full_visitor_id
WHERE ((a.unit_price / 1000000)* a.units_sold) IS NOT NULL 
GROUP BY al.country, al.city
ORDER BY transaction_revenue DESC
```
```sql
SELECT al.country AS country, 
		SUM((a.unit_price / 1000000)* a.units_sold) AS transaction_revenue
FROM all_sessions al
JOIN analytics a ON al.full_visitor_id = a.full_visitor_id
WHERE ((a.unit_price / 1000000)* a.units_sold) IS NOT NULL 
GROUP BY al.country
ORDER BY transaction_revenue DESC
```
## Answer: 
Using these queries we can find that the United States heavily impacts the revenue generated significantly
more than any other country. 







