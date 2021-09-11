# Learn PostgreSQL Tutorial - Full Course for Beginners

## Create a database
In the `psql` terminal:
```SQL
CREATE DATABASE test;
```
Describe the database:
```
\d
```
List all databases:
```
\l
```
Connect to a particular database:
```
\c test
```
Drop a database:
```
DROP DATABASE test; -- !!!DANGEROUS DON'T DO THIS!!!
```

## Create and insert data into a database table
We can use lowercase as well, but it is a good convention to use uppercase for SQL terms.
```SQL
CREATE TABLE person (
 id BIGSERIAL NOT NULL PRIMARY KEY,
 first_name VARCHAR(50) NOT NULL,
 last_name VARCHAR(50) NOT NULL,
 email VARCHAR(150),
 gender VARCHAR(20) NOT NULL,
 date_of_birth DATE NOT NULL,
 country_of_birth VARCHAR(50)
);
```
`BIGSERIAL` auto-increments the id whenever a new row is added. `NOT NULL` indicates that the entry can not be Null.

Describe the table:
```
\d person
```
Insert data into table:
```SQL
INSERT INTO person (first_name, last_name, email, gender, date_of_birth, country_of_birth) VALUES ('Phil', 'Mundell', 'pmundell0@weebly.com', 'Agender', DATE '2021-05-22', 'Thailand');
```
We can also insert multiple rows into a table by inserting from a `.sql` file that contains lots of `INSERT INTO` commands like above. In the postgres `psql` terminal:
```
\i path_to_sql_file.sql
```
[Mockaroo](https://www.mockaroo.com/) can be used to generate mock data.

Drop a table:
```
DROP TABLE person; -- !!!DANGEROUS DON'T DO THIS!!!
```

## Fetch data using SQL

1. Select everything from a table 
	```SQL
	SELECT * FROM person;
	```
2. Select particular column from a table and `DISTINCT` (unique values)
	```SQL
	SELECT first_name FROM person;
	
	SELECT DISTINCT first_name FROM person;
	```
3. Select particular column from a table and sort in ascending or descending order (default is ascending order)
	```SQL
	SELECT first_name FROM person ORDER BY first_name;
	SELECT first_name FROM person ORDER BY first_name ASC;
	SELECT first_name FROM person ORDER BY first_name DESC;
	SELECT first_name FROM person ORDER BY (id, email) DESC;
	```
4. Filtering using the `WHERE` clause
	```SQL
	SELECT first_name FROM person WHERE first_name = 'Salman';
	SELECT first_name FROM person WHERE first_name = 'Salman' 
	AND country_of_birth = 'Pakistan';
	SELECT first_name FROM person WHERE first_name = 'Salman' 
	AND (country_of_birth = 'Pakistan' OR 'Switzerland') 
	AND last_name <> 'Maqbool'; -- <> denotes not equal
	```
5. Comparison operators
	```SQL
	SELECT 1 = 1;
	SELECT 1 = 2;
	SELECT 1 > 2;
	SELECT 1 < 2;
	SELECT 1 >= 2;
	SELECT 1 <= 2;
	SELECT 1 <> 2; -- For not equal
	```
6. Select only a particular number of results
	```SQL
	SELECT * FROM person LIMIT 5; -- First 5 resuls
	SELECT * FROM person OFFSET 5 LIMIT 5; -- Results 6-10
	SELECT * FROM person OFFSET 5; -- All but the first 5 results
	```
	We can also use the `FETCH` keyword as `LIMIT` is not in the SQL standards.
	```SQL
	SELECT * FROM person OFFSET 5 FETCH FIRST 5 ROW ONLY;
	SELECT * FROM person OFFSET 5 FETCH FIRST ROW ONLY;
	```
7. The `IN` keyword
	```SQL
	SELECT * FROM person WHERE country_of_birth = 'France' 
	OR country_of_birth = 'China' OR country_of_birth = 'Brazil'
	SELECT * FROM person WHERE country_of_birth 
	IN ('China', 'Brazil', 'France');
	```
	Both the above statements are equivalent.
8. The `BETWEEN` keyword
	```SQL
	SELECT * FROM person
	WHERE date_of_birth
	BETWEEN DATE '2000-01-01' AND '2015-12-31'
	```
9. `LIKE` and `ILIKE`
	```SQL
	SELECT * FROM person WHERE email LIKE '%.com' -- % is the wildcard character
	SELECT * FROM person WHERE email LIKE '%gmail.%'
	SELECT * FROM person WHERE email LIKE '______@%' 
	/* _ represents a single character match, so this will only 
	return emails that have six character before @ */
	SELECT * FROM person WHERE email LIKE '___adj@%'
	
	SELECT * FROM person WHERE country_of_birth ILIKE 'p%'
	/* ILIKE ignores the case-sensitivity */
	```
10. `GROUP BY` and `COUNT`
	```SQL
	SELECT country_of_birth, COUNT(*) FROM person GROUP BY country_of_birth;
	```
	Returns all distinct `country_of_birth` and their respective counts.
11. `HAVING`
	```SQL
	SELECT country_of_birth, COUNT(*) FROM person 
	GROUP BY country_of_birth HAVING COUNT(*) > 5
	ORDER BY COUNT(*) DESC;
	```

## Create a new table
A table for cars

```SQL
CREATE TABLE car (
 id BIGSERIAL NOT NULL PRIMARY KEY,
 make VARCHAR(100) NOT NULL,
 model VARCHAR(100) NOT NULL,
 price NUMERIC(19, 2) NOT NULL
);
```

## More SQL
1. `MAX` and `MIN`
	```SQL
	SELECT MAX(price) FROM car;
	SELECT MIN(price) FROM car;
	SELECT make, model, MIN(price) FROM car GROUP BY make, model;
	/* Returns the minimum prize for each made and model */

	SELECT make, MAX(price) FROM car GROUP BY make;
	/* Returns max price for each make */
	```
2. `AVG` and `ROUND`
	```SQL
	SELECT AVG(price) FROM car;
	SELECT ROUND(AVG(price)) FROM car; -- Rounds to the nearest integer
	```
3. `SUM`
	```SQL
	SELECT SUM(price) FROM car;
	SELECT make, SUM(price) FROM car GROUP BY make;
	/* Returns make and sum of price for each make */
	```
4. Arithmetic operations
	```SQL
	SELECT 10 + 2;
	SELECT 10 - 2;
	SELECT 10 * 2;
	SELECT 10 / 2;
	SELECT 10 * 2 + 8;
	SELECT 10^2;
	SELECT 5!; -- Factorial
	SELECT 10 % 3; -- Remainder
	
	SELECT id, make, model, price, ROUND(price * 0.9, 2) FROM car;
	/* ROUND(price * 0.9, 2) returns the value to 2 decimal places */
	```
5. Alias `AS`
	```SQL
	SELECT id, 
	make, 
	model, 
	price AS original_price, 
	ROUND(price * 0.1, 2) AS discount, 
	ROUND(price - (price * 0.1), 2) AS discounted_price 
	FROM car;
	```
6. `COALESCE`: Used to return a default value in case the requested value is `null`
	```SQL
	SELECT COALESCE(1) AS number; -- Would return 1
	SELECT COALESCE(null, 1) AS number; -- Would return 1
	SELECT COALESCE(null, null, 10) AS number; 
	/* Would go through all null values until it sees a valid
	value i.e. 10 and return that */
	
	SELECT COALESCE(null, null, 1, 10) AS number; -- Would return 1
	
	SELECT COALESCE(email, 'Email not provided') from person;
	```
7. `NULLIF`: Used to handle division by 0. More generally, it takes two arguments, and returns the first argument if the first argument is not equal to the second argument. Otherwise, if the arguments are same, it would return `null`.
	```SQL
	SELECT 10 / 0; -- Would give a division by zero error
	SELECT 10 / NULL; -- Does not return an error, rather returns null
	
	SELECT NULLIF(10, 10); -- Would return null
	SELECT NULLIF(10, 1); -- Would return 10
	
	SELECT 10 / NULLIF(0,0); -- Would return Null instead of an error
	SELECT COALESCE(10 / NULLIF(0,0), 0) -- Would return 0 instead of null or error
	```
8. `NOW` and `INTERVAL`: Working with dates and timestamps.
	```SQL
	SELECT NOW(); 
	/* Gives the time right now. Example 2021-09-11 07:33:58.71247+05 */
	
	SELECT NOW()::DATE;
	/* Casts to just the DATE part and returns that. Example 2021-09-11 */
	
	SELECT NOW()::TIME; -- Return the time
	
	SELECT NOW() - INTERVAL '1 YEAR'; -- Current date - 1 year
	SELECT NOW() - INTERVAL '10 YEARS';
	SELECT NOW() - INTERVAL '10 YEAR'; -- Can used both YEARS and YEAR
	SELECT NOW() - INTERVAL '10 MONTH';
	SELECT NOW() - INTERVAL '10 DAY';
	SELECT NOW() + INTERVAL '10 YEAR';
	SELECT NOW()::DATE + INTERVAL '10 YEAR'; -- Would return time as well as 00:00:00
	SELECT (NOW() + INTERVAL '10 YEAR')::DATE; -- Would return only the date
	
	SELECT EXTRACT(YEAR FROM NOW());
	/* We can substitute YEAR with CENTURY, Y, MONTH, DAY / D,
	DOY (day of the year), DOW (day of the week) HOUR / H, 
	MINUTE / MIN / M, SECOND / SEC / S, MILLISECOND / MS */
	```
9. `AGE`: Takes two arguments, the "current" date and the "date of birth":
	```SQL
	SELECT 
	first_name, 
	last_name, 
	country_of_birth, 
	date_of_birth, 
	AGE(NOW(), date_of_birth) as age 
	FROM person;
	
	SELECT 
	first_name, 
	last_name, 
	country_of_birth, 
	date_of_birth, 
	EXTRACT(YEAR FROM AGE(NOW(), date_of_birth)) as age 
	FROM person;
	```
	The first query extracts age in the timestamp format, while the second one only chooses year from the age and returns that.

## More on Primary Keys
	
## Reference
[Learn PostgreSQL Tutorial - Full Course for Beginners](https://www.youtube.com/watch?v=qw--VYLpxG4) by [freecodecamp](https://www.freecodecamp.org/)

Instructor(s): [Amigoscode](https://amigoscode.com/)
