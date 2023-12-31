### Query 1.
Create database ```books_db```.

```
CREATE DATABASE books_db
```

### Query 2.
Create table ```book``` with columns as per the attached schema.

```CREATE TABLE book (
	book_id 		INT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
	title 			VARCHAR(100) NOT NULL,
	title_other	    VARCHAR(100),
	isbn 			VARCHAR(20) NOT NULL,
	publisher 	    VARCHAR(50),
	year 		    SMALLINT,
	language 		VARCHAR(50) NOT NULL,
	pages 		    SMALLINT,
	form 		    VARCHAR(50) NOT NULL,
	status 		    VARCHAR(20) NOT NULL
);
```

### Query 3.
Create table ```author``` with columns as per the attached schema.

```
CREATE TABLE author (
	author_id	    INT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
	name		    VARCHAR(50) NOT NULL,
	country		    VARCHAR(50),
	date_birth	    DATE,
	date_death	    DATE
);
```

### Query 4.
Create junction table ```book_author``` with columns as per the attached schema.

```
CREATE TABLE book_author (
	book_id     INT NOT NULL,
	author_id   INT NOT NULL,
	CONSTRAINT fk_book_author_book
		FOREIGN KEY(book_id)
			REFERENCES book(book_id),
	CONSTRAINT fk_book_author_author
		FOREIGN KEY(author_id)
			REFERENCES author(author_id),
	UNIQUE(book_id, author_id)
);
```

### Query 5.
Create table ```genre``` with columns as per the attached schema.

```
CREATE TABLE genre (
	genre_id	INT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
	name 	    VARCHAR(50)
);
```

### Query 6.
Create junction table ```book_genre``` with columns as per the attached schema.

```
CREATE TABLE book_genre (
	book_id		INT NOT NULL,
	genre_id	INT NOT NULL,
	CONSTRAINT fk_book_genre_book
		FOREIGN KEY(book_id)
			REFERENCES book(book_id),
	CONSTRAINT fk_book_genre_genre
		FOREIGN KEY(genre_id)
			REFERENCES genre(genre_id),
	UNIQUE(book_id, genre_id)
);
```

### Query 7.
Create table ```series``` with columns as per the attached schema.

```
CREATE TABLE series (
	series_id	INT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
	title		VARCHAR(100)
);
```

### Query 8.
Create junction table ```book_series``` with columns as per the attached schema.

```
CREATE TABLE book_series (
	series_id	INT NOT NULL,
	book_id		INT NOT NULL,
	volume		INT,
	CONSTRAINT fk_book_series_series
		FOREIGN KEY(series_id)
			REFERENCES series(series_id),
	CONSTRAINT fk_book_series_book
		FOREIGN KEY(book_id)
			REFERENCES book(book_id),
	UNIQUE(series_id, book_id)
);
```

### Query 9.
In table ```book```, rename column  ```title_other``` to ```title_orig```.

```
ALTER TABLE book 
RENAME COLUMN title_other TO title_orig;
```

### Query 10.
In table ```book```, remove the NOT NULL constraint from column ```isbn```.

```
ALTER TABLE book
ALTER COLUMN isbn DROP NOT NULL;
```

### Query 11.
In table ```book```, change the data type of column ```language``` from VARCHAR(50) to VARCHAR(10).

```
ALTER TABLE book
ALTER COLUMN language TYPE VARCHAR(10);
```

### Query 12.
Drop the ```series``` table.

```
DROP TABLE IF EXISTS series;
```

### Query 13.
In table ```author```, remove column ```country```.

```
ALTER TABLE author
DROP COLUMN IF EXISTS country;
```

### Query 14.
In table ```author```, add column ```country``` (type VARCHAR(50), no constraints).

```
ALTER TABLE author 
ADD COLUMN country VARCHAR(50);  
```