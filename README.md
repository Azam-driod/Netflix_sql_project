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
create table netflix  (show_id varchar(7),
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

...sql
select * from netflix
...

### 1. Count the number of Movies vs TV Shows

...sql
select type,
count(*) as total_content from netflix 
group by type
...

### 2. Find out the most common rating for movies and TV shows

...sql
select
  type,
  rating
 from
 (
select type, 
rating,
count(*),
rank() over(partition by type order by count(*) desc) as ranking
from netflix 
group by type, rating
) as t1
where 
ranking = 1
...


### 3. List all movies released in a specific year (e,g., 20)

...sql
 select type, 
 release_year from netflix 
 where type = 'Movie' and release_year = 2020
 ...

### 4. Find the top 5 countries with the most content on netflix.

...sql
select unnest(string_to_array(country, ',')) as new_country, 
count(show_id) as total_content
 from netflix
 group by new_country
 order by total_content desc
 limit 5
 ...

 ### 5. Identify the longest movie or TV show duration.

 ...sql
 select * from netflix 
  where type = 'Movie'
  and duration = (select max(duration) from netflix)
  ...

### 6. Find content added in the last 5 years.

...sql
select * from netflix
  where 
  to_date(date_added, 'Month DD, YYYY') >= current_date - interval '5 years'
  ...

### 7. Find all the moviea/TV shows by director 'Rajiv Chilaka'.

...sql
select type , title , director
from netflix
where director ilike '%Rajiv Chilaka%'
...


### 8. List all TV shows with more than 5 seasons.

...sql
select *
from netflix
where type = 'TV Show'
and
split_part(duration, ' ', 1)::numeric > 5
...

### 9. Count the number of content items in each genre.

...sql
select 
unnest(string_to_array(listed_in, ',')) as genre,
count (show_id) as total_content
from netflix
group by genre
...


### 10. Find each year and the average numbers of content release in India on netflix,return top 5 year with highest avg content release.

...sql
select 
extract(year from to_date(date_added,'Month DD, YYYY')) as year,
count(*) as yearly_content,
round(
count(*)::numeric/(select count(*) from netflix where country = 'India')::numeric * 100,2) as avg_content_per_year
from netflix 
where country = 'India'
group by year
...


### 11. List all movies that are documentaries.

...sql
select type,listed_in 
from netflix 
where type = 'Movie' 
and listed_in ilike '%Documentaries%'
...


### 12. Find all content without a director.

...sql
select * from netflix
where director is null
...


### 13. Find how many movies actor 'Salman Khan' appeared in last 10 years

...sql
select * from netflix
where casts ilike '%Salman Khan%'
and release_year > extract(year from current_date) - 10
...


### 14. Find the top 10 actors who have appeared in the highest number of movies produced in India.

...sql
select 
unnest(string_to_array(casts, ',')) as actors,
count(*) as total_content
from netflix
where country ilike '%India%'
group by actors
order by total_content desc
limit 10
...

### 15. Categorize the content based on the presence of the keywords 'Kill' and 'Violence' in the description field. Label content containing these keywords as 'Bad' and all other content as 'Good'. Count how many items fall into each category.

...sql
with new_table
as
(
select *,
case
when description ilike '%Kill%' or
description ilike '%violence%' then 'Bad_Content'
else 'Good_Content' 
end category from netflix
)
select category,
 count(*) as total_content
 from new_table
 group by category
 ...

 ## Objective:
 Categorize content as 'Bad' if it contains 'kill' or 'violence' and 'Good' otherwise. Count the number of items in each category.

 ## Findings and Conclusion
 - Content Distribution: The dataset contains a diverse range of movies and TV shows with varying ratings and genres.
 - Common Ratings: Insights into the most common ratings provide an understanding of the content's target audience.
 - Geographical insights: The top countries and the average content release by India highlight regional content distribution.
 - Content Categorization: Categorizing content based on specific keywords helps in understanding the nature of  content available on Netflix.
