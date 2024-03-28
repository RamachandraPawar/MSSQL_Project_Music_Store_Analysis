select *from [dbo].[album]
select *from [dbo].[album2]
select*from[dbo].[artist]
select *from [dbo].[customer]
select *from [dbo].[employee]
select *from[dbo].[genre]
select *from [dbo].[invoice]
select *from [dbo].[invoice_line]
select *from [dbo].[media_type]
select *from [dbo].[playlist]
select *from [dbo].[playlist_track]
select *from [dbo].[track]

----EASY

----1. Who is the senior most employee based on job title? 
select  [employee_id], CONCAT([first_name],' ', last_name) AS Full_name,[title]
from[dbo].[employee]
WHERE [reports_to] IS NULL

--2nd method

select top 1 [employee_id], CONCAT([first_name],' ', last_name) AS Full_name,[title]
from[dbo].[employee]
order by [levels] desc

-- 2. Which countries have the most Invoices? 

select [billing_country],count([billing_country]) as total_counts
from [dbo].[invoice]
group by [billing_country]
order by count([billing_country]) desc

--3. What are top 3 values of total invoice? 

select top 3 [billing_country],count([billing_country]) as total_counts,round(sum([total]),2) as toal_value
from [dbo].[invoice]
group by [billing_country]
order by count([billing_country]) desc

/*4. Which city has the best customers? We would like to throw a promotional Music 
Festival in the city we made the most money. Write a query that returns one city that 
has the highest sum of invoice totals. Return both the city name & sum of all invoice 
totals*/

select top 1 [billing_city] ,count([billing_city]) as total_counts,round(sum([total]),2) as toal_value
from [dbo].[invoice]
group by [billing_city]
order by count([billing_city]) desc

/*5. Who is the best customer? The customer who has spent the most money will be 
declared the best customer. Write a query that returns the person who has spent the 
most money */

select top 1  concat([dbo].[customer].[first_name],'  ',[dbo].[customer].[last_name]) as Full_name,.[customer].[customer_id],round(sum([dbo].[invoice].total),2) as total_spent
from [dbo].[customer]
inner join [dbo].[invoice] on [dbo].[invoice].customer_id=[dbo].[customer].customer_id
group by [dbo].[customer].[first_name],[customer].[customer_id],[dbo].[customer].[last_name]
order by sum([dbo].[invoice].total) desc

--MODERATE

/*1. Write query to return the email, first name, last name, & Genre of all Rock Music 
listeners. Return your list ordered alphabetically by email starting with A*/

select distinct [dbo].[customer].[customer_id], [first_name],[last_name],[email],[dbo].[genre].name
from [dbo].[customer]
join [dbo].[invoice] on [dbo].[invoice].customer_id=[dbo].[customer].customer_id
join [dbo].[invoice_line] on [dbo].[invoice_line].invoice_id=[dbo].[invoice].invoice_id
join [dbo].[track] on [dbo].[track].track_id=[dbo].[invoice_line].track_id
join [dbo].[genre] on [dbo].[genre].genre_id=[dbo].[track].genre_id
where [dbo].[genre].name='ROCK'
order by [email]

/*2. Let's invite the artists who have written the most rock music in our dataset. Write a 
query that returns the Artist name and total track count of the top 10 rock bands */

select top 10 [dbo].[artist].[artist_id] ,[dbo].[artist].name,count([dbo].[track].name) as total_songs,[dbo].[genre].name
from [dbo].[artist]
join [dbo].[album] on [dbo].[album].artist_id=[dbo].[artist].artist_id
join [dbo].[track] on [dbo].[track].album_id=[dbo].[album].album_id
join [dbo].[genre] on [dbo].[genre].genre_id=[dbo].[track].genre_id
where [dbo].[genre].name='Rock'
group by [dbo].[artist].name,[dbo].[genre].name,[dbo].[artist].[artist_id]
order by count([dbo].[track].name) desc

/*3. Return all the track names that have a song length longer than the average song length. 
Return the Name and Milliseconds for each track. Order by the song length with the 
longest songs listed first*/

select [name],[milliseconds]
from [dbo].[track]
where  [milliseconds] > (select avg([milliseconds]) from [dbo].[track])
order by [milliseconds] desc

-----ADVANCE

/*1. Find how much amount spent by each customer on artists? Write a query to return 
customer name, artist name and total spent  */

select [dbo].[customer].[customer_id], concat([first_name],' ',[last_name]) as full_name,round(sum([dbo].[invoice_line].[unit_price]*[dbo].[invoice_line].quantity),2) as total_spent,[dbo].[artist].name
from  [dbo].[customer]
join [dbo].[invoice] on [dbo].[invoice].customer_id=[dbo].[customer].customer_id
join [dbo].[invoice_line] on [dbo].[invoice_line].invoice_id=[dbo].[invoice].invoice_id
join [dbo].[track] on [dbo].[track].track_id=[dbo].[invoice_line].track_id
join [dbo].[album] on [dbo].[album].album_id=[dbo].[track].album_id
join [dbo].[artist] on [dbo].[artist].artist_id=[dbo].[album].artist_id
group by [dbo].[artist].name,concat([first_name],' ',[last_name]),[dbo].[customer].[customer_id]
order by sum([dbo].[invoice_line].[unit_price]) desc,[dbo].[artist].name

/*2. We want to find out the most popular music Genre for each country. We determine the 
most popular genre as the genre with the highest amount of purchases. Write a query 
that returns each country along with the top Genre. For countries where the maximum 
number of purchases is shared return all Genres   */

with table_1 as(
select  [dbo].[genre].[name],[dbo].[invoice].billing_country,count([dbo].[invoice].invoice_id) as total_sold,
dense_rank() over(partition by [dbo].[invoice].billing_country order by count([dbo].[invoice].invoice_id)desc) as rank
from [dbo].[genre]
join [dbo].[track] on [dbo].[track].genre_id=[dbo].[genre].genre_id
join [dbo].[invoice_line] on [dbo].[invoice_line].track_id=[dbo].[track].track_id
join [dbo].[invoice] on [dbo].[invoice].invoice_id=[dbo].[invoice_line].invoice_id
join  [dbo].[customer] on [dbo].[customer].customer_id=[dbo].[invoice].customer_id
group by [dbo].[genre].[name],[dbo].[invoice].billing_country,[dbo].[customer].[country]
) 

select  * from  table_1 where rank =1

/*3. Write a query that determines the customer that has spent the most on music for each 
country. Write a query that returns the country along with the top customer and how 
much they spent. For countries where the top amount spent is shared, provide all 
customers who spent this amount  */

with table_2 as(
select [dbo].[customer].[customer_id],
	   concat([first_name],' ',[last_name]) as full_name,
       round(sum([dbo].[invoice].[total]),2) as total_spent,
       [billing_country],
	   dense_rank() over(partition by  [billing_country]  order by sum([dbo].[invoice].[total])  desc) as rank
 from  [dbo].[customer] 
join [dbo].[invoice] on [dbo].[invoice].customer_id=[dbo].[customer].customer_id
group by concat([first_name],' ',[last_name]),[billing_country], [dbo].[customer].[customer_id])

select * from table_2 where rank = 1


