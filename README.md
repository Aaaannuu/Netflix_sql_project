# Netflix_sql_project

## Overview
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.

## Objectives
Analyze the distribution of content types (movies vs TV shows).
Identify the most common ratings for movies and TV shows.
List and analyze content based on release years, countries, and durations.
Explore and categorize content based on specific criteria and keywords.

## Dataset

## Schema 


create table netflix(
                    show_id	varchar(7),
                    type	varchar(8),
                    title	varchar(150),
                    director	varchar(208),
                    casts	varchar(1000),
                    country	varchar(150),
                    date_added	varchar(50),
                    release_year	int,
                    rating	varchar(10),
                    duration	varchar(15),
                    listed_in	varchar(100),
                    description varchar(270)
);


## Business Problems and Solutions

                          select * from netflix;

# 1. Count the number and percentage of types of Movies vs TV Shows 
                      Select 
                              type, 
			      count(*) as type_count,
	                      count(*) * 100/(select count(*) from netflix)
                     from netflix
                     group by type;

# 2. Find the most common rating for movies and TV shows

                       with cte as (select 
                                  type,
                                  rating,
	                          count(*),
	                          dense_rank() over(partition by type order by count(*) desc)rn
                       from netflix
                       group by 1,2)
                       Select type,
		       rating
                       from cte 
                       where rn =1;
**Objective**: Identify the most frequently occurring rating for each type of content.

# 3. List all movies released in a specific year (e.g., 2020)
                         select title from netflix
                             where type = 'Movie' and release_year = 2020;

**Objective**: Retrieve all movies released in a specific year.

# 4. Find the top 5 countries with the most content on Netflix

select 
      unnest(string_to_array(country,',')) as new_country,
	  count(show_id) as total_content
from netflix
group by 1
order by 2 desc
limit 5;

**Objective**: Identify the top 5 countries with the most content items.

# 5. Identify the longest movie

select *
from netflix
where type = 'Movie'
and duration = (select max(duration) from netflix);

**Objective**: Find the movie with the longest duration.

# 6. Find all the movies/TV shows by director 'Rajiv Chilaka'!

SELECT * from netflix
where director like '%Rajiv Chilaka%';

**Objective**: Retrieve content added to Netflix in the last 5 years.

# 7. Find content added in the last 5 years

select *,
       TO_DATE(date_added,'Month DD,YYYY') AS NEW_DATE
from netflix
where TO_DATE(date_added,'Month DD,YYYY') >= current_date - interval '5years';

**Objective**: List all content directed by 'Rajiv Chilaka'.

# 8. List all TV shows with more than 5 seasons
Select * 
from netflix
where type = 'TV Show' and duration > '5 Seasons';
**or** 
select *      
from netflix
where type = 'TV Show' and
split_part(duration,' ',1)::numeric >5;

**Objective**: Identify TV shows with more than 5 seasons.

# 9. Count the number of content items in each genre
select 
     unnest(string_to_array(listed_in, ',')) as genre,
	  count(*)
from netflix
group by 1
order by 2 desc;

**Objective**: Count the number of content items in each genre.

# 10. Find each year and the average numbers of content release in India on netflix. 
 return top 5 year with highest avg content release!
select
	   extract (year from to_date(date_added,'Month DD,YYYY')) AS date,
	   round(count(*)::numeric/ (select count(*) from netflix where country = 'India')::numeric *100,0) as average
from netflix
where country = 'India'
group by 1
order by average desc
limit 5;

**Objective**: Calculate and rank years by the average number of content releases by India.

# 11. List all movies that are documentaries

select * from netflix
where  listed_in  like '%Documentaries%';

**Objective**: Retrieve all movies classified as documentaries.

# 12. Find all content without a director

SELECT * FROM NETFLIX
WHERE director is null;

**Objective**: List content that does not have a director.

# 13.Find how many movies actor 'Salman Khan' appeared in last 10 years!
SELECT * FROM NETFLIX
where casts Ilike '%Salman khan%'
and release_year >= extract(year from current_date) - 10 ;

**Objective**: Count the number of movies featuring 'Salman Khan' in the last 10 years.

# 14. Find the top 10 actors who have appeared in the highest number of movies produced in India.

select 
	  unnest(string_to_array(casts,',')) as actors,
	  count(title)
from netflix
where country Ilike '%India%'
group by 1
order by count(title) desc
limit 10;

**Objective**: Identify the top 10 actors with the most appearances in Indian-produced movies.

# 15. Categorize the content based on the presence of the keywords 'kill' and 'violence' in 
The description field. Label content containing these keywords as 'Bad' and all other content as 'Good'. Count how many items fall into each category.

select 
case 
      when description Ilike '%kill%' or description Ilike '%violence%' then 'Bad'
      else 'Good'
end as category, 
count(*)
from netflix
group by 1;

**Objective**: Categorize content as 'Bad' if it contains 'kill' or 'violence' and 'Good' otherwise. Count the number of items in each category.

## Findings and Conclusion

Content Distribution: The dataset contains a diverse range of movies and TV shows with varying ratings and genres.
Common Ratings: Insights into the most common ratings provide an understanding of the content's target audience.
Geographical Insights: The top countries and the average content releases by India highlight regional content distribution.
Content Categorization: Categorizing content based on specific keywords helps in understanding the nature of content available on Netflix.
