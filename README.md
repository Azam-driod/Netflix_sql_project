# Netflix movies and TV Shows Data Analysis using SQL.
  <img width="567" height="261" alt="netflix s" src="https://github.com/user-attachments/assets/067ec010-8bfa-499d-bbda-d97281cc06d4" />

## Overview

This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings and conclutions.

## Objectives

- Analysis the distribution of content types (movies vs TV shows).
- Identify the most common ratings for movies and TV shows.
- List and analyze content based on release years, countries and durations.
- Explore and categorize content based on specific criteria and keywords.


## Dataset

The data for this project is sourced from the kaggle dataset:
- **Dataset Link:** [Movies Dataset](https://www.kaggle.com/code/omar61103yasser/netflix-movies-and-tv-shows)

## Schema

'''sql
create table netflix  
(  
 show_id           varchar(7),  
 type              varchar(10),  
 title             varchar(150),  
 director          varchar(208),  
 casts             varchar(1000),  
 country           varchar(150),  
 date_added        varchar(50) ,  
 release_year      int,  
 rating            varchar(20),  
 duration          varchar(12),  
 listed_in         varchar(25),  
 description       varchar(300)  
);  
'''  

'''sql
select * from netflix;  
'''

## Business problems and solutions

### 1. Count the number of Movies vs TV Shows

'''sql  
 SELECT TYPE,  
     COUNT(*) as total_content FROM netflix   
   GROUP BY type;    
'''  

### 2. Find out the most common rating for movies and TV shows

'''sql  
SELECT  
  type,  
  rating  
 FROM  
 (  
SELECT type,   
rating,  
COUNT(*),  
rank() over(partition by type order by count(*) desc) as ranking  
FROM netflix   
GROUP by type, rating  
) as t1  
WHERE   
ranking = 1;    
'''    


### 3. List all movies released in a specific year (e,g., 20)

'''sql  
 SELECT type,   
 release_year FROM netflix   
 WHERE type = 'Movie' AND release_year = 2020;    
 '''

### 4. Find the top 5 countries with the most content on netflix.

'''sql  
SELECT UNNEST(string_to_array(country, ',')) AS new_country,   
COUNT(show_id) AS total_content  
 FROM netflix  
 GROUP BY new_country  
 ORDER BY total_content desc  
 limit 5;    
 '''

 ### 5. Identify the longest movie or TV show duration.

 '''sql  
 SELECT * FROM netflix   
  WHERE type = 'Movie'  
  AND duration = (select max(duration) FROM netflix);    
  '''

### 6. Find content added in the last 5 years.

'''sql  
SELECT * FROM netflix  
  WHERE   
  to_date(date_added, 'Month DD, YYYY') >= current_date - interval '5 years';    
  '''

### 7. Find all the moviea/TV shows by director 'Rajiv Chilaka'.

'''sql  
SELECT type , title , director  
FROM netflix  
WHERE director ILIKE '%Rajiv Chilaka%';    
'''


### 8. List all TV shows with more than 5 seasons.

'''sql  
SELECT *  
FROM netflix  
WHERE type = 'TV Show'  
AND  
SPLIT_PART(duration, ' ', 1)::numeric > 5;   
'''

### 9. Count the number of content items in each genre.

'''sql  
SELECT   
UNNEST(string_to_array(listed_in, ',')) AS genre,  
COUNT (show_id) AS total_content  
FROM netflix  
GROUP BY genre;    
'''


### 10. Find each year and the average numbers of content release in India on netflix,return top 5 year with highest avg content release.

'''sql  
SELECT   
EXTRACT(year from to_date(date_added,'Month DD, YYYY')) as year,  
COUNT(*) as yearly_content,  
ROUND(  
COUNT(*)::numeric/(select count(*) from netflix where country = 'India')::numeric * 100,2) as avg_content_per_year  
FROM netflix  
WHERE country = 'India'  
GROUP BY year;    
'''


### 11. List all movies that are documentaries.

'''sql  
SELECT type,listed_in   
FROM netflix  
WHERE type = 'Movie'   
AND listed_in ILIKE '%Documentaries%';    
'''


### 12. Find all content without a director.

'''sql  
SELECT * FROM netflix  
WHERE director is null;    
'''


### 13. Find how many movies actor 'Salman Khan' appeared in last 10 years

'''sql  
SELECT * FROM netflix  
WHERE casts ILIKE '%Salman Khan%'  
AND release_year > extract(year from current_date) - 10;    
'''


### 14. Find the top 10 actors who have appeared in the highest number of movies produced in India.

'''sql  
SELECT  
UNNEST(string_to_array(casts, ',')) AS actors,  
COUNT(*) AS total_content  
FROM netflix  
WHERE country ILIKE '%India%'  
GROUP BY actors  
ORDER BY total_content desc  
limit 10;  
'''

### 15. Categorize the content based on the presence of the keywords 'Kill' and 'Violence' in the description field. Label content containing these keywords as 'Bad' and all other content as 'Good'. Count how many items fall into each category.

'''sql  
WITH new_table  
AS  
(  
SELECT *,  
CASE  
WHEN description ILIKE '%Kill%' or  
description ILIKE '%violence%' then 'Bad_Content'  
ELSE 'Good_Content'   
END category FROM netflix  
)  
SELECT category,  
 COUNT(*) as total_content  
 FROM new_table  
 GROUP BY category;    
 '''  

 ## Objective:  
 Categorize content as 'Bad' if it contains 'kill' or 'violence' and 'Good' otherwise. Count the number of items in each category.  

 ## Findings and Conclusion
 - Content Distribution: The dataset contains a diverse range of movies and TV shows with varying ratings and genres.
 - Common Ratings: Insights into the most common ratings provide an understanding of the content's target audience.
 - Geographical insights: The top countries and the average content release by India highlight regional content distribution.
 - Content Categorization: Categorizing content based on specific keywords helps in understanding the nature of  content available on Netflix.
