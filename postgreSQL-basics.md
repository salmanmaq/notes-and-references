# Learn PostgreSQL Tutorial - Full Course for Beginners

Uses PostgresQL-12

## Create a database
In the `postgres` `psql` terminal:
```SQL
CREATE DATABASE test;
```
Describe the database:
```
\d
```
Describe only the database tables:
```
\dt
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
`BIGSERIAL` auto-increments the id whenever a new row is added. `NOT NULL` indicates that the entry can not be Null. See more about [BIGSERIAL and sequences](#more-about-bigserial-and-sequences).

Describe the table:
```
\d person
```
`INSERT` data into table:
```SQL
INSERT INTO person (first_name, last_name, email, gender, date_of_birth, country_of_birth) VALUES ('Phil', 'Mundell', 'pmundell0@weebly.com', 'Agender', DATE '2021-05-22', 'Thailand');
/* Use single quotes '' for text, not double quotes "" */
```
We can also insert multiple rows into a table by inserting from a `.sql` file that contains lots of `INSERT INTO` commands like above. In the `psql` terminal:
```
\i path_to_sql_file.sql
```
[Mockaroo](https://www.mockaroo.com/) can be used to generate mock data. We can generate data multiple times and concatenate it manually.

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
For tables with a `PRIMARY KEY`, we can't have multiple entries with the same primary key. The below example would give a duplicate value error, where the value should be unique across the column.
```SQL
INSERT INTO person (
 id,
 first_name,
 last_name,
 gender,
 date_of_birth,
 country_of_birth
 ) VALUES(1, 'John', 'Doe', 'Male', DATE '2020-12-31', 'Hong Kong');
 ```
 We can, however, drop this uniqueness constraint by `ALTER`ing the table to remove the said constraint.
 ```SQL
 ALTER TABLE person DROP CONSTRAINT person_pkey;
 ```
 This would drop the constraint and now the query mentioned above would work.
 
 We can add the primary key again:
 ```SQL
 ALTER TABLE person ADD PRIMARY KEY (id)
 /* We can pass an array of column names here to compose a PRIMARY KEY 
 based off multiple columns */
```
This would not work if there are duplicate values in the intended `PRIMARY KEY` column and would return an error. We can do that by removing one or more of the rows where the uniqueness constraint is defied. We can [DELETE records](#delete-records) to remove the problematic entries. The command should work fine afterwards.

## `UNIQUE` constraint
```SQL
ALTER TABLE person ADD CONSTRAINT unique_email_address UNIQUE (email);
/* We name the constraint as unique_email constraint. We can also pass 
multiple columns to the array. */
```
This ensures that all records have a unique email address. Adding a non-unique value for email in a new record will return an error.

Drop the `UNIQUE` constraint
```SQL
ALTER TABLE person DROP CONSTRAINT unique_email_address;
```

Another method to create a `UNIQUE` constraint:
```SQL
ALTER TABLE person ADD UNIQUE (email);
```
Using this method, `postgres` defines the constraint name itself. In this case, the constraint name would be:
```
person_email_key | <table_name>_<column_name>_key)
```


## `CHECK` constraint
Allows to check against a boolean condition. For example, it could be used to specify what values a particular column accepts. Suppose we want to allow only specific values of `country_of_birth`:
```SQL
ALTER TABLE person ADD CONSTRAINT country_of_birth_constraint CHECK (country_of_birth IN ('Pakistan, China, US, Brazil, India, Indonesia, Germany'));
ALTER TABLE person ADD CONSTRAINT country_of_birth_constraint CHECK (country_of_birth <> 'Wakanda');
```
The add constraint will fail if there's already data in the table which violates the specified constraint.

## `DELETE` records
```SQL
DELETE FROM person; -- !!!DANGEROUS - DELETES ALL RECORDS IN TABLE!!!
DELETE FROM person WHERE id = 1;
DELETE FROM person WHERE gender = 'Female' AND country_of_birth='Nigeria';
```

See Also: [Foreign keys > Deleting a record with a foreign key](#deleting-a-record-with-a-foreign-key)

## `UPDATE` records
The `SET` keyword
```SQL
UPDATE person SET email = 'jane@doe.com' WHERE id = 10;
UPDATE person SET email = 'jane@doe.com';
/* DANGEROUS - Not using a WHERE clause will update all records */

UPDATE person SET first_name = 'Jane', last_name = 'Doe', gender = 'Female'
WHERE id = 10;
```

## `ON CONFLICT` `DO NOTHING`
Allows us to handle conflict scenarios such as duplicate key errors i.e. where a column is either a `PRIMARY KEY` or has a `UNIQUE` constraint. Doing this results in not throwing an error and no records inserted either.
```SQL
INSERT INTO person (
 id,
 first_name,
 last_name,
 email,
 gender,
 date_of_birth,
 country_of_birth
 ) VALUES(1, 'John', 'Doe', 'john@doe.com', 'Male', DATE '2020-12-31', 'Australia')
 ON CONFLICT (id) DO NOTHING;
 ```

## Upsert
However, when there is a conflict, we might not want to `DO NOTHING`. Suppose the `email` column has a `UNIQUE` constraint and a record with the email `john@doe.com` has already been inserted with id `1002`. Suppose the user mistakenly entered an incorrect email and tries to enter a correct email now i.e. `johndoe@gmail.com`. We can process such as scenario as:
```SQL
INSERT INTO person (
 id, 
 first_name,
 last_name,
 email,
 gender,
 date_of_birth,
 country_of_birth
 ) VALUES(1002, 'John', 'Doe', 'johndoe@gmai.com', 'Male', DATE '2020-12-31', 'Australia')
 ON CONFLICT (id) DO UPDATE SET email = EXCLUDED.email;
 ```
 `DO UPDATE` and `EXCLUDED` keywords. We can update multiple values as well:
 ```SQL
 INSERT INTO person (
 id, 
 first_name,
 last_name,
 email,
 gender,
 date_of_birth,
 country_of_birth
 ) VALUES(1002, 'Doe', 'John', 'doe@john.com', 'Male', DATE '2020-12-31', 'Australia')
 ON CONFLICT (id) DO UPDATE SET email = EXCLUDED.email, 
 first_name = EXCLUDED.first_name, last_name = EXCLUDED.last_name;
```
This is probably not a good example of Upsert. I think a better example with Microsoft SQL Server can be found [here](https://towardsdatascience.com/python-to-sql-upsert-safely-easily-and-fast-17a854d4ec5a).
	
## Foreign keys
Dropped tables `person` and `car` so that we can recreate new tables with the appropriate schema. We use the `REFERENCE` keyword to link the `car_id` column in table `person` with `id` in the table `car`.
```SQL
CREATE TABLE car (
 id BIGSERIAL NOT NULL PRIMARY KEY,
 make VARCHAR(100) NOT NULL,
 model VARCHAR(100) NOT NULL,
 price NUMERIC(19, 2) NOT NULL
);
/* The car table needs to be created first as the person table 
references it. */

CREATE TABLE person (
 id BIGSERIAL NOT NULL PRIMARY KEY,
 first_name VARCHAR(50) NOT NULL,
 last_name VARCHAR(50) NOT NULL,
 email VARCHAR(150),
 gender VARCHAR(20) NOT NULL,
 date_of_birth DATE NOT NULL,
 country_of_birth VARCHAR(50),
 car_id BIGINT REFERENCES car (id),
 UNIQUE(car_id) /* Makes the car_id unique i.e. one car can only be owned
 by a single person */
);
```
Create some records for the `person` table without associating with a `car_id` for now:
```SQL
INSERT INTO person (
 first_name,
 last_name,
 gender,
 email,
 date_of_birth,
 country_of_birth
) VALUES ('John', 'Doe', 'Male', 'john@doe.com', DATE '1994-12-23', 'Pakistan');

INSERT INTO person (
 first_name,
 last_name,
 gender,
 email,
 date_of_birth,
 country_of_birth
) VALUES ('Jane', 'Doe', 'Female', 'jane@doe.com', DATE '1992-04-21', 'United States');

INSERT INTO person (
 first_name,
 last_name,
 gender,
 email,
 date_of_birth,
 country_of_birth
) VALUES ('Janetta', 'Doelle', 'Nonbinary', 'janetta@doelle.com', DATE '2000-11-03', 'Ghana');

INSERT INTO person (
 first_name,
 last_name,
 gender,
 date_of_birth,
 country_of_birth
) VALUES ('John', 'Smith', 'Male', DATE '1984-04-30', 'England');
```
Create some records for the `car` table:
```SQL
INSERT INTO car (
 make,
 model,
 price
) VALUES ('Land Rover', 'Sterling', '87665.38');
 
INSERT INTO car (
 make,
 model,
 price
) VALUES ('Honda', 'Civic', '15731.14');
 
INSERT INTO car (
 make,
 model,
 price
) VALUES ('Suzuki', 'Mehran', '5000.00');

INSERT INTO car (
 make,
 model,
 price
) VALUES ('Mazda', 'RX-8', '51272.48');
```
Assign cars to people:
```SQL
UPDATE person SET car_id = 2 WHERE id = 1;
UPDATE person SET car_id = 45 WHERE id = 1;
/* Will throw an error as car_id = 45 does not exist in the car table */

UPDATE person SET car_id = 2 WHERE id = 2;
/* This will give an error as car_id has a UNIQUE constraint */
```

### Deleting a record with a foreign key
If we have a `person` with a `car_id`, we would not be able to delete that `car` record as it would be referenced in the person table. That would result in a foreign key constraint error. Do do that, we would first need to update that specific person record and set the `car_id` to `NULL` such that we "de-reference" or "disassociate" that car from any person. Once we have done that, we should be able to DELETE the said `car` record. For example:
```SQL
UPDATE person SET car_id = 4 WHERE id = 4;
/* First associate a car with a person */

DELETE FROM car WHERE id = 4;
/* This would thow an error as it is linked to person with id = 4 */

UPDATE person SET car_id = NULL WHERE id = 4;
/* First SET the relevant car_id to NULL */

DELETE FROM car WHERE id = 4;
/* We should be able to delete the car record now */
```

## Inner `JOIN`
Used to combine two tables and return **records that have a foreign key present in both tables**
```SQL
SELECT * FROM person
JOIN car ON person.car_id = car.id;
```
Sometimes the results may not display so well so we might want to turn on expanded display. It can be toggled on and off using:
```
\x
```
We can `SELECT` specific columns from both tables:
```SQL
SELECT person.first_name, car.make, car.model, car.price
FROM person
JOIN car ON person.car_id = car.id;
```

## `LEFT JOIN`
Combines two tables such that it includes all the records from the left table as well as all corresponding records from the right table that have a matching foreign key. This query would not return any records in the right table that do not have have any association with the left table. In this case, the `person` table is the left table and the `car` table is the right table.
```SQL
SELECT * FROM person
LEFT JOIN car ON car.id = person.car_id;
```
This query would return all persons. Records of cars that are associated with any person via a `car_id` would be appended to the respective person. But those persons without a `car_id` value, or without a match for `person.car_id = car.id`, would not have any car details appended to their records. Those cars that do not have their `car.id` in a `person.car_id` would not be returned.

To return persons who don't have a car, but with the (empty) car columns appended:
```SQL
SELECT * FROM person
LEFT JOIN car ON car.id = person.car_id
WHERE car.* IS NULL;
```

## `RIGHT JOIN`
Similar to `LEFT JOIN` but returns all records from the right table but only records from the left table that have any association with the right table (i.e. a matching foreign key) would be returned.
```SQL
SELECT * FROM person
RIGHT JOIN car ON car.id = person.car_id;
```
This query would return all cars. Records of persons that are associated with any car via a `car_id` would be appended to the respective car. But those persons without a `car_id` value, or without a match for `person.car_id = car.id`, would not be appended to any car record. Those persons without a matching `person.car_id = car.id` would not be returned.

## Export query results to CSV
```SQL
/* Syntax: \copy (query) TO 'output_file_path.csv' DELIMITER ',' CSV HEADER; */
\copy (SELECT * FROM person LEFT JOIN car ON person.car_id = car.id ORDER BY person.id) TO 'output_file_path.csv' DELIMITER ',' CSV HEADER;
```

## Export query results to JSON
```SQL
/* Syntax: 
\t -- Toggles tuple only mode (Turn on)
\a -- Toggled aligned output mode (Toggle unaligned)
\o path_to_result.json -- Output file name (\o again for terminal output)
SELECT JSON_AGG(t) FROM (SELECT * FROM table_name) t; -- SELECT JSON_AGG(t) FROM (query) t;
\*/
\t
\a
\o path_to_result.json 
SELECT JSON_AGG(t) FROM (SELECT * FROM person LEFT JOIN car ON car.id = person.car_id ORDER BY person.id) t;
```
[Reference](https://dba.stackexchange.com/questions/90482/export-postgres-table-as-json)

## Export query results to XML
```SQL
/* Syntax: 
SELECT QUERY_TO_XML (
 'query as text in single quotes',
 nulls, -- (true or false) To include null values or not
 tableforest, (true or false) Each row in separate xml document or not
 targetns -- Target namespace
);
\*/
\o path_to_result.xml
SELECT QUERY_TO_XML(
 'SELECT * from person
 LEFT JOIN car ON car.id = person.car_id
 ORDER BY person.id', 
 true,
 false,
 ''
);
```
[Reference](https://kags.me.ke/post/how-to-export-data-from-database-toxml/)

## More about `BIGSERIAL` and sequences
`BIGSERIAL` creates a `BIGINT` data type, but additionally creates a sequence and a `nextval` function that auto-increments the value for that column. For example, in the `person` table, the `id` has an associated Default `nextval('person_id_seq'::regclass)`. We can check for sequences using:
```
\ds
```
We can also `SELECT` from sequences:
```SQL
SELECT * FROM person_id_seq;
SELECT * FROM car_id_seq;
/* These return the last_value, the log_cnt (number of times called), 
and is_called (indicating whether the sequence has been called or not) */

SELECT last_value from person_id_seq;

SELECT nextval('person_id_seq'::regclass);
/* Calls the function nextval on the person_id_seq and increments 
last_value by 1. We can check using below */
SELECT * FROM person_id_seq;
```
If we now add another person, they would get an `id` based on the incremented `last_value`
```SQL
INSERT INTO person (
 first_name,
 last_name,
 gender,
 date_of_birth,
 country_of_birth
) VALUES ('Alexa', 'Jones', 'Female', DATE '2009-01-18', 'China');
```
To reset the sequence to a desired value:
```SQL
ALTER SEQUENCE person_id_seq RESTART WITH 5;
/* This changes person_id_seq.last_value to 5 */
```

## Extensions
Functions that can add extra functionality to your database.

Check list of all available extensions:
```SQL
SELECT * FROM pg_available_extensions;
```
The `uuid-ossp` extension can be used to generate UUIDs. Install the extension using:
```SQL
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
```
To generate a UUID, we have to call a function. To examine the list of available functions:
```
\df
```
To generate a UUID, we can select a UUID generation function from the functions listed above and invoke it:
```SQL
SELECT uuid_generate_v4();
```
UUIDs are a great candidate for `PRIMARY KEY` in tables as they are more secure (difficult to guess). They also make migrating data across databases much easier as they are globally unique.

Create new tables to use UUID as `PRIMARY KEY`:
```SQL
```SQL
CREATE TABLE car (
 car_uid UUID NOT NULL PRIMARY KEY,
 make VARCHAR(100) NOT NULL,
 model VARCHAR(100) NOT NULL,
 price NUMERIC(19, 2) NOT NULL
);
/* The car table needs to be created first as the person table 
references it. */

CREATE TABLE person (
 person_uid UUID NOT NULL PRIMARY KEY,
 first_name VARCHAR(50) NOT NULL,
 last_name VARCHAR(50) NOT NULL,
 email VARCHAR(150),
 gender VARCHAR(20) NOT NULL,
 date_of_birth DATE NOT NULL,
 country_of_birth VARCHAR(50),
 car_uid UUID REFERENCES car (car_uid),
 UNIQUE(car_uid), /* Makes the car_id unique i.e. one car can only be owned
 by a single person */
 UNIQUE(email)
);
```
`INSERT` records into tables:
```SQL
INSERT INTO person (
 person_uid,
 first_name,
 last_name,
 gender,
 email,
 date_of_birth,
 country_of_birth
) VALUES (uuid_generate_v4(), 'John', 'Doe', 'Male', 'john@doe.com', DATE '1994-12-23', 'Pakistan');

INSERT INTO person (
 person_uid,
 first_name,
 last_name,
 gender,
 email,
 date_of_birth,
 country_of_birth
) VALUES (uuid_generate_v4(), 'Jane', 'Doe', 'Female', 'jane@doe.com', DATE '1992-04-21', 'United States');

INSERT INTO person (
 person_uid,
 first_name,
 last_name,
 gender,
 email,
 date_of_birth,
 country_of_birth
) VALUES (uuid_generate_v4(), 'Janetta', 'Doelle', 'Nonbinary', 'janetta@doelle.com', DATE '2000-11-03', 'Ghana');


INSERT INTO car (
 car_uid,
 make,
 model,
 price
) VALUES (uuid_generate_v4(), 'Land Rover', 'Sterling', '87665.38');

INSERT INTO car (
 car_uid,
 make,
 model,
 price
) VALUES (uuid_generate_v4(), 'Honda', 'Civic', '15731.14');

INSERT INTO car (
 car_uid,
 make,
 model,
 price
) VALUES (uuid_generate_v4(), 'Mazda', 'RX-8', '51272.48');
```
Add cars to persons:
```SQL
UPDATE person SET car_uid = '74314404-c37c-448e-94b8-fac4c81b2e32' WHERE person_uid = '550b2f29-21a8-43bb-a17c-eb1b626e5ae0';
UPDATE person SET car_uid = '8078a5e5-d501-48a4-8ce1-c392e80945b9' WHERE person_uid = '2c04379c-416b-4f3c-9e94-469ec6c8eed8';
```
Since since both the primary key and foreign key for `car_uid` are now same (in both tables - i.e. in `person.car_uid` and `car.car_uid`), we can person `JOIN` using a different syntax (`USING`):
```SQL
SELECT * FROM person
JOIN car USING (car_uid);
SELECT * FROM person
LEFT JOIN car USING (car_uid);
```

## Reference
[Learn PostgreSQL Tutorial - Full Course for Beginners](https://www.youtube.com/watch?v=qw--VYLpxG4) by [freecodecamp](https://www.freecodecamp.org/)

Instructor(s): [Amigoscode](https://amigoscode.com/)
