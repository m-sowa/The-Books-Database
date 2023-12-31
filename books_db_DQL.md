### Query 1.
Get a book by title. Fetch related information from all tables.

```
SELECT 
	book.*, 
	author.*, 
	genre.*, 
	series.*, 
	book_series.*
FROM book
LEFT JOIN book_author 
	ON book.book_id = book_author.book_id
LEFT JOIN author 
	ON book_author.author_id = author.author_id
JOIN book_genre 
	ON book.book_id = book_genre.book_id
JOIN genre 
	ON book_genre.genre_id = genre.genre_id
LEFT JOIN book_series 
	ON book.book_id = book_series.book_id
LEFT JOIN series 
	ON book_series.series_id = series.series_id
WHERE book.title = '1984';
```

### Query 2.
Get all books by a given author.

```
SELECT 
	book.title, 
	book.isbn, 
	book.publisher, 
	book.status, 
	author.name, 
	series.title
FROM book
JOIN book_author
	ON book.book_id = book_author.book_id
JOIN author
	ON book_author.author_id = author.author_id
LEFT JOIN book_series
	ON book.book_id = book_series.book_id
LEFT JOIN series
	ON book_series.series_id = series.series_id
WHERE author.name LIKE '%Tolkien'
ORDER BY year DESC;
```

### Query 3.
Get all books by a given genre.

```
SELECT 
	b.title, 
	b.publisher, 
	b.language, 
	a.name, 
	g.name
FROM book AS b
LEFT JOIN book_author AS ba 
	ON b.book_id = ba.book_id
LEFT JOIN author AS a 
	ON ba.author_id = a.author_id
JOIN book_genre AS bg 
	ON b.book_id = bg.book_id
JOIN genre AS g 
	ON bg.genre_id = g.genre_id
WHERE g.name = 'history'
ORDER BY title;
```

### Query 4.
Get all books from a given series.

```
SELECT DISTINCT 
	s.title AS series_title, 
	b.title AS book_title, 
	bs.volume, 
	a.name
FROM book AS b
LEFT JOIN book_author AS ba 
	ON b.book_id = ba.book_id
LEFT JOIN author AS a 
	ON ba.author_id = a.author_id
LEFT JOIN book_series AS bs 
	ON b.book_id = bs.book_id
LEFT JOIN series AS s 
	ON bs.series_id = s.series_id
WHERE s.title = 'The Lord of the Rings'
ORDER BY volume;
```

### Query 5.
Get all books by living authors (ordered from oldest to youngest).

```
SELECT 
	b.title, 
	a.name, 
	a.country, 
	a.date_birth
FROM book AS b
JOIN book_author AS ba 
	ON b.book_id = ba.book_id
JOIN author AS a 
	ON ba.author_id = a.author_id
WHERE a.name IS NOT NULL 
	AND a.date_death IS NULL
ORDER BY a.date_birth;
```

### Query 6.
Get the total number of pages read (finished books only).

```
SELECT 
	SUM(pages) AS pages_total
FROM book
WHERE status = 'finished';
```

### Query 7.
Get the number of books read in English and in Polish.

```
SELECT
	COUNT(CASE WHEN language = 'EN' THEN 1 END) AS en_books_total,
	COUNT(CASE WHEN language = 'PL' THEN 1 END) AS pl_books_total
FROM book;
```

### Query 8.
Get the book with most pages by language (excluding the Bible with id 31).

```
SELECT 
	language, 
	MAX(pages) AS longest_book
FROM book
WHERE book_id != 31
GROUP BY language;
```

### Query 9.
Get the average number of pages by genre (excluding the Bible with id 31).

```
SELECT 
	g.name, 
	ROUND(AVG(b.pages), 0) AS avg_length
FROM book AS b
JOIN book_genre AS bg 
	ON b.book_id = bg.book_id
JOIN genre AS g 
	ON bg.genre_id = g.genre_id
WHERE b.book_id != 31
GROUP BY g.name
ORDER BY avg_length DESC;
```

### Query 10.
Get the ages of the authors and information if author is living or deceased.

```
SELECT 
	name,
	(CASE
		WHEN date_death IS NOT NULL THEN 'no'
		WHEN date_death IS NULL THEN 'yes'
		END) AS living,
	(CASE
		WHEN date_death IS NOT NULL THEN DATE_PART('year', date_death) - DATE_PART('year', date_birth)
		WHEN date_death IS NULL THEN DATE_PART('year', current_date) - DATE_PART('year', date_birth)
		END) AS age
FROM author
ORDER BY age DESC;
```

### Query 11.
Get the highest and lowest age from among living and deceased authors.

```
WITH author_status AS (
	SELECT 
		name, 
		(CASE
			WHEN date_death IS NOT NULL THEN 'living'
			ELSE 'deceased'
		END) AS status,
		(CASE
			WHEN date_death IS NOT NULL THEN DATE_PART('year', date_death) - DATE_PART ('year', date_birth)
			ELSE DATE_PART('year', current_date) - DATE_PART ('year', date_birth)
		END) AS age
	FROM author
)
SELECT 
	status, 
	MAX(age) AS oldest, 
	MIN(age) AS youngest
FROM author_status
GROUP BY status;
```

### Query 12.
Get the number of books by the country of origin of their authors.

```
SELECT 
	a.country, 
	COUNT(*) AS books_by_country
FROM book AS b
JOIN book_author AS ba 
	ON b.book_id = ba.book_id
JOIN author AS a 
	ON ba.author_id = a.author_id
GROUP BY a.country
ORDER BY a.country;
```

### Query 13.
Get the number of books by genre (note: each book is assigned to more than 1 genre).

```
SELECT 
	g.name, 
	COUNT(b.title) AS books_by_genre
FROM book AS b
JOIN book_genre AS bg 
	ON b.book_id = bg.book_id
JOIN genre AS g 
	ON bg.genre_id = g.genre_id
GROUP BY g.name
ORDER BY books_by_genre DESC;
```

### Query 14.
Get the top 2 most common genres and their count, excluding 'fiction' and 'non-fiction'.

```
SELECT 
	g.name, 
	COUNT(*) AS books_by_genre
FROM book AS b
JOIN book_genre AS bg 
	ON b.book_id = bg.book_id
JOIN genre AS g 
	ON bg.genre_id = g.genre_id
WHERE g.name NOT IN ('fiction', 'non-fiction')
GROUP BY g.name
ORDER BY books_by_genre DESC
LIMIT 2;  
```

### Query 15.
Get the year of the oldest and most recently published book for each publisher. Exclude cases when “oldest” and “most recent” would be the same year. 

```
SELECT 
	publisher, 
	MAX(year) as most_recent, 
	MIN(year) oldest
FROM book
GROUP BY publisher
HAVING MAX(year) != MIN(year)
ORDER BY publisher;
```

### Query 16.
Get the title of the oldest and most recently published book for each publisher. For publishers with a single book, show only that book.

```
SELECT * 
FROM (SELECT 
		publisher, 
		title, 
		year,
		ROW_NUMBER() OVER(PARTITION BY publisher ORDER BY year DESC) AS rn1,
		ROW_NUMBER() OVER(PARTITION BY publisher ORDER BY year) AS rn2
	FROM book) AS x
WHERE x.rn1 = 1 OR x.rn2 = 1;
```

### Query 17.
Get the titles of books longest than the average length of all the books in the database (without showing the average).

```
SELECT 
	title, 
	pages
FROM book
WHERE pages > (SELECT AVG(pages)
			  	FROM book)
ORDER BY pages;
```

### Query 18.
Get the titles of books longest than the average length of all the books in the database (show the average as well).

```
WITH book_length AS (
	SELECT 
		ROUND(AVG(pages), 0) AS avg_length
	FROM book
)
SELECT 
	title, 
	pages, 
	(SELECT avg_length FROM book_length) AS avg_pages
FROM book
WHERE pages > (SELECT avg_length FROM book_length)
ORDER BY pages;
```

### Query 19.
Get the titles of all books – show the original title (title_orig) if available; if not, show the translated title (title).

```
SELECT 
	COALESCE(title_orig, title) AS book_title
FROM book;
```

### Query 20.
Get the longest and shortest book per language (PL and EN).

```
SELECT * 
FROM (SELECT 
		title, 
		language, 
		pages,
		ROW_NUMBER() OVER(PARTITION BY language ORDER BY pages DESC) AS rn1,
		ROW_NUMBER() OVER(PARTITION BY language ORDER BY pages) AS rn2
	FROM book
	WHERE title NOT LIKE 'Pismo%') AS x
WHERE x.rn1 = 1 OR x.rn2 = 1;
```

### Query 21.
Get the earliest and latest born author from each country (include only countries with more than 1 author).

```
SELECT 
	name, 
	country, 
	birth_year
FROM
	(SELECT 
		name, 
		country, 
		DATE_PART('year', date_birth) AS birth_year,
		RANK() OVER(PARTITION BY country ORDER BY date_birth) AS born_earliest,
	 	RANK() OVER(PARTITION BY country ORDER BY date_birth DESC) AS born_latest
	FROM author) AS x
WHERE (x.born_earliest = 1 OR x.born_latest = 1)
	AND x.born_earliest != x.born_latest;
```

### Query 22.
Compare the length of each book against the average length for each genre. Show if a book is “shorter”, “longer” or “same”. Show the difference in length between the book and the genre average. 

```
WITH avg_by_genre AS (
	SELECT 
		g.name, 
		ROUND(AVG(b.pages), 0) AS avg_length
	FROM book AS b
	JOIN book_genre AS bg 
		ON b.book_id = bg.book_id
	JOIN genre AS g 
		ON bg.genre_id = g.genre_id
	GROUP BY g.name
)
SELECT 
	g.name, 
	b.title, 
	b.pages, 
	ag.avg_length,
	(CASE 
		WHEN b.pages > ag.avg_length THEN 'longer'
		WHEN b.pages < ag.avg_length THEN 'shorter'
		ELSE 'same'
		END) AS comparison,
	(CASE 
		WHEN b.pages > ag.avg_length THEN b.pages - ag.avg_length
		WHEN b.pages < ag.avg_length THEN ag.avg_length - b.pages
		ELSE 0
		END) AS difference
FROM book AS b
JOIN book_genre AS bg 
	ON b.book_id = bg.book_id
JOIN genre AS g 
	ON bg.genre_id = g.genre_id
JOIN avg_by_genre AS ag 
	ON g.name = ag.name
ORDER BY g.name, comparison, difference DESC;
```

### Query 23.
Get the titles of books ordered by book length (descending), together with next by length and difference between the two.

```
SELECT 
	title, 
	pages,
	LEAD(pages) OVER(ORDER BY pages DESC) AS next_by_length,
	pages - (LEAD(pages) OVER(ORDER BY pages DESC)) AS length_diff
FROM book;
```

### Query 24.
Get books published between the years 2000 and 2015 (inclusive).

```
SELECT 
	title, 
	isbn, 
	publisher, 
	year
FROM book
WHERE year BETWEEN 2000 AND 2015
ORDER BY year;
```

### Query 25.
Get the number of books with 10-digit and 13-digit ISBN (excluding books without ISBN).

```
SELECT
	COUNT(CASE WHEN LENGTH(isbn) = 10 THEN 1 END) AS short_isbn,
	COUNT(CASE WHEN LENGTH(isbn) = 13 THEN 1 END) AS long_isbn
FROM book
WHERE isbn IS NOT NULL;
```

### Query 26.
Get the titles of books from series, together with series titles (combined in one column).

```
SELECT 
	CONCAT(b.title, ' from the series ', s.title) AS book_from_series
FROM book AS b
JOIN book_series AS bs 
	ON b.book_id = bs.book_id
JOIN series AS s 
	ON bs.series_id = s.series_id
ORDER BY s.title, b.title;
```

### Query 27.
Get the upper-cased author name and book title (combined in one column).

```
SELECT 
	UPPER(a.name) || ', ' || b.title AS author_and_book
FROM book AS b
JOIN book_author AS ba 
	ON b.book_id = ba.book_id
JOIN author AS a 
	ON ba.author_id = a.author_id
ORDER BY a.name;
```

### Query 28.
Get a list of all book titles joined with all series titles in one column.

```
SELECT 
	title
FROM book
UNION
SELECT 
	title
FROM series
ORDER BY title;
```