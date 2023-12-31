### Query 1.
Manually insert a new book and populate relevant tables with the following information: 
*title*: “Wolfheart”, *ISBN*: 9781451605761, *publisher*: Pocket Books, *year*: 2012, *language*: EN, *pages*: 455, *form*: paperback, *status*: finished, *author*: Richard A. Knaak, *author’s country*: United States, *author’s DoB*: 1961-05-28, *book series*: “World of Warcraft”, *genre*: fiction, fantasy
[note: 'Wolfheart' - book_id 37; 'Richard A. Knaak' - author_id 29; 'World of Warcraft' - series_id 7; 'fiction' - genre_id 1; 'fantasy' - genre_id 16]

```
INSERT INTO book
	VALUES(default, 'Wolfheart', null, '9781451605761', 'Pocket Books', 2012, 'EN', 455, 'paperback', 'finished');

INSERT INTO author
	VALUES(default, 'Richard A. Knaak', 'United States', '1961-05-28', null);
	
INSERT INTO series
	VALUES(default, 'World of Warcraft');

INSERT INTO book_author
	VALUES(37,29);
	
INSERT INTO book_series
	VALUES(7,37, null);
	
INSERT INTO book_genre
	VALUES(37, 1),
			(37, 16);
```

### Query 2.
Delete duplicate authors (by name and country) using a self-join.

```
DELETE FROM author
WHERE author_id IN (SELECT a2.author_id
					FROM author a1
					JOIN author a2 ON a1.name = a2.name AND a1.country = a2.country
					WHERE a1.author_id < a2.author_id);
```

### Query 3.
Delete duplicate authors (by name and country) using a window function.

```
DELETE FROM author
WHERE author_id IN (SELECT 
						author_id
					FROM (SELECT *,
							ROW_NUMBER() OVER (PARTITION BY name, country) AS rn
						FROM author) AS x
					WHERE x.rn > 1);
```

### Query 4.
Update the length (pages) of the book “Robinson Crusoe” from 304 to 305.

```
UPDATE book
SET pages = 305
WHERE title LIKE 'Robinson%';
```
