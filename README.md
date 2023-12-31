# The-Books-Database

Since I love reading, I created my own database books_db, which serves as a collection of data about books that I am reading, planning to read, or have read. 

The database was created using PostgreSQL version 16.1.

## Schema
The database consists of seven tables, organised as shown in the schema below:
1. ***book*** - table with data about books;
1. ***author*** - table with data about book authors;
1. ***book_author*** - junction table connecting book and author tables;
1. ***genre*** - table with a list of book genres;
1. ***book_genre*** - junction table connecting book and genre tables;
1. ***series*** - table with series to which the books belong;
1. ***book_series*** - junction table connecting book and series tables.

## Assumptions
My assumptions when designing the database were as follows:
* *book - author*: there can be a book without any author, but there is no author without at least one book;
* *book - genre*: each book has at least one genre and there is no genre which is not assigned to any book;
* *book - series*: there can be a book not belonging to any series, but there is no series without a book assigned to it.

Thus, the schema covers three types of relationships (marked using Crow's foot notation):
* one mandatory to many optional;
* one mandatory to many mandatory;
* one mandatory to one optional.

## Skills practiced
Here's what the books_db database has helped me practice so far:
- designing a basic entity relationship diagram;
- using DDL, DML, DQL statements for CRUD operations; 
- creating and deleting a database;
- creating, dropping, and altering a table;
- modifying constraints on existing tables;
- inserting data into tables manually and by importing a CSV file;
- writing basic SQL queries to extract data;
- using different types of operators: arithmetic, comparison, logical;
- using different types of predicates, e.g. LIKE, (NOT) IN, (NOT) BETWEEN, IS (NOT) NULL;
- filtering and sorting data using DISTINCT, WHERE, GROUP BY, HAVING, LIMIT;
- using joins (inner, outer, self) and UNION;
- using subqueries;
- using CTE (WITH statement);
- using aggregate functions: COUNT, SUM, AVG, MAX, MIN;
- using scalar functions, e.g. ROUND, LENGTH, UPPER, CONCAT;
- using date functions, e.g. CURRENT_DATE, DATE_PART;
- using conditional functions, e.g. CASE, COALESCE;
- using window functions, e.g. RANK, ROW_NUMBER, LEAD.

I have been using the books_db database to practice writing different types SQL queries. My scripts can be found in the folder attached to this project. I'm aware that most of the queries can be written in more than one way. Since I'm a beginner, I have not been attempting to choose the most optimal way to write the quesries. Rather, I created them alongside the learning process. 

## Queries
Here's a list of sample queries created so far:

### DDL statements:
1. Create a database called “books_db”.
1. Create a table named “book” with columns as per the attached schema.
1. Create a table named “author” with columns as per the attached schema.
1. Create a junction table named “book_author” with columns as per the attached schema.
1. Create a table named “genre” with columns as per the attached schema.
1. Create a junction table named “book_genre” with columns as per the attached schema.
1. Create a table named “series” with columns as per the attached schema.
1. Create a junction table named “book_series” with columns as per the attached schema.
1. In table “book”, rename column  “title_other” to “title_orig”.
1. In table “book”, remove the NOT NULL constraint from column “isbn”.
1. In table “book”, change the data type of column “language” from VARCHAR(50) to VARCHAR(10).
1. Drop the “series” table.
1. In table “author”, remove the column “country”.
1. In table “author”, add column “country” (type VARCHAR(50), no constraints).

### DML statements:
1. Manually insert a new book and populate relevant tables with the information given.
1. Delete duplicate authors (by name and country) using a self-join.
1. Delete duplicate authors (by name and country) using a window function.
1. Update the length (pages) of the book “Robinson Crusoe” from 304 to 305.

### DQL statements:
1. Get a book by title – fetch related information from all tables.
1. Get all books by a given author.
1. Get all books by a given genre.
1. Get all books from a given series.
1. Get all books by living authors (ordered from oldest to youngest).
1. Get the total number of pages read (finished books only).
1. Get the number of books read in English and in Polish.
1. Get the book with most pages by language (excluding the Bible with id 31).
1. Get the average number of pages by genre (excluding the Bible with id 31).
1. Get the ages of the authors and information if author is living or deceased.
1. Get the highest and lowest age from among living and deceased authors.
1. Get the number of books by the country of origin of their authors.
1. Get the number of books by genre (note: each book is assigned to more than 1 genre).
1. Get the top 2 most common genres and their count, excluding 'fiction' and 'non-fiction'.
1. Get the year of the oldest and most recently published book for each publisher. Exclude cases when “oldest” and “most recent” would be the same year. 
1. Get the title of the oldest and most recently published book for each publisher. For publishers with a single book, show only that book.
1. Get the titles of books longest than the average length of all the books in the database (without showing the average).
1. Get the titles of books longest than the average length of all the books in the database (show the average as well).
1. Get the titles of all books – show the original title (title_orig) if available; if not, show the translated title (title).
1. Get the longest and shortest book per language (PL and EN).
1. Get the earliest and latest born author from each country (include only countries with more than 1 author).
1. Compare the length of each book against the average length for each genre. Show if a book is “shorter”, “longer” or “same”. Show the difference in length between the book and the genre average.
1. Get the titles of books ordered by book length (descending), together with next by length and difference between the two.
1. Get books published between the years 2000 and 2015 (inclusive).
1. Get the number of books with 10-digit and 13-digit ISBN (excluding books without ISBN).
1. Get the titles of books from series, together with series titles (combined in one column).
1. Get the upper-cased author name and book title (combined in one column).
1. Get a list of all book titles joined with all series titles in one column.
