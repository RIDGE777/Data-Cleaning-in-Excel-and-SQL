# Data-Cleaning-in-Excel-and-SQL
In this project I took a small dataset of movies and cleaned the dataset for analysis. These are the steps I took to clean the data:

### 1. SQL
I first uploaded the messy_data dataset into SQL Server Management Studio, where I ran a SELECT statement to view the data. 
After identifing column titles I created a new table, Clean_data, to insert data into.
For easy upload of data into Clean_data, I had to convert my datatypes into VARCHAR(255).
I ran the SELECT function to view all the data in the messy_data table and then copied the dirty data into ChatGPT for cleaning
This was rather easy because of the size of my dataset but not feaseable for a large dataset.
I ran the SELECT function and copied all the data into a blank Excel worksheet

### 2. Excel
These are the cleaning steps I undertook in Excel:
- I converted release_year into date format, MM/DD/YYYY: Ctrl + 1 - Number - Date -Type
- I aligned all data in the sheet into Center across selection: Ctrl + 1 - Alignment - Text alignment
- I converted the income column into currency: Ctrl + 1 - Number - Currency
- I then saved the Excel worksheet as movie_data and imported it into SQL Management Server Studio for further cleaning as movie_data table

### 3. SQL
I took the following cleaning steps:

- CHECK FOR DUPLICATES- 1 duplicate
        
        SELECT COUNT(*)
        FROM DataCleaning..movie_data
        GROUP BY imdb_id
        HAVING COUNT(*) > 1

- DELETE DUPLICATES
        
        WITH CTE AS (
          SELECT [imdb_id], ROW_NUMBER() OVER (PARTITION BY [imdb_id] ORDER BY [imdb_id]) as row_num
            FROM DataCleaning..movie_data
        )
        DELETE FROM CTE WHERE row_num > 1 

- Change #N/A in content_rating into N/A
        
        UPDATE DataCleaning..movie_data
        SET content_rating = 'N/A'
        WHERE content_rating = '#N/A'

- Remove leading and trailing spaces in the genre column and replace space with ' / ' where genre > 1 word
        
        UPDATE DataCleaning..movie_data
        SET genre = REPLACE(REPLACE(LTRIM(RTRIM(genre)), '  ', ' '), ' ', ' / ')

- Remove the trailing 00:00:00.000 in the release_year column
        
        ALTER TABLE DataCleaning..movie_data
        ALTER COLUMN release_year DATE;

- Remove decimal points from the income column
        
        ALTER TABLE DataCleaning..movie_data
        ALTER COLUMN income BIGINT;

- Remove decimal points from the votes column
This code multiplies the number by a power of 10 to remove the decimal point, depending on the number of decimal places in the original number. 
The case statement checks the number of decimal places and returns the appropriate power of 10 to multiply by. 
The result is then cast to an integer.

        UPDATE DataCleaning..movie_data
        SET votes = CAST(votes * POWER(10, CASE
                                                WHEN votes - FLOOR(votes) = 0 THEN 0
                                                WHEN votes * 10 - FLOOR(votes * 10) = 0 THEN 1
                                                WHEN votes * 100 - FLOOR(votes * 100) = 0 THEN 2
                                                ELSE 3
                                            END) AS INT);
                                            

- Count and replace NULLS with correct data in release_year column
        
        SELECT COUNT (*) FROM DataCleaning..movie_data-3 NULLS
        WHERE release_year IS NULL 

- View the NULLS
        
        SELECT * FROM DataCleaning..movie_data
        WHERE release_year IS NULL;
        
These were the NULLS:

          1. tt0468569	The Dark Knight	NULL	Action / Crime / Drama	152	USA	PG-13	Christopher Nolan	1005455211	2241615	9
          2. tt0086250	Scarface	NULL	Crime / Drama	170	USA	R	Brian De Palma	66023585	721343	7.8
          3. tt0075314	Taxi Driver	NULL	Crime / Drama	114	USA	R	Martin Scorsese	28441292	703264	7.7*/

- Fill the NULLS

        UPDATE DataCleaning..movie_data
        SET release_year = '2008-07-18' 
        WHERE release_year IS NULL AND imdb_id = 'tt0468569';

        UPDATE DataCleaning..movie_data
        SET release_year = '1983-12-09'
        WHERE release_year IS NULL AND imdb_id = 'tt0086250';

        UPDATE DataCleaning..movie_data
        SET release_year = '1976-02-08'
        WHERE release_year IS NULL AND imdb_id = 'tt0075314';

- Count and replace NULLS with correct data in duration column- 6 rows
        
        --Count NULLS in duration column
        SELECT COUNT (*) FROM DataCleaning..movie_data
        WHERE duration IS NULL 

        --View the NULLS
        SELECT * FROM DataCleaning..movie_data
        WHERE duration IS NULL;

These were the NULLS:

          1. tt0110912	Pulp Fiction	1994-10-28	Crime / Drama	NULL	USA	R	Quentin Tarantino	222831817	1780147	8
          2. tt0108052	Schindler's List	1994-03-11	Biography / Drama / History	NULL	USA	R	Steven Spielberg	322287794	1183248	8.9
          3. tt0137523	Fight Club	1999-10-29	Drama	NULL	UK	R	David Fincher	101218804	1807440	8.8
          4. tt0133093	The Matrix	1999-05-07	Action / Sci-Fi	NULL	USA	R	Lana Wachowski Lilly Wachowski	465718588	1632315	8.7
          5. tt0080684	Star Wars: Episode V - The Empire Strikes Back	1980-09-19	Action / Adventure / Fantasy	NULL	USA	PG	Irvin Kershner	549265501	1132073	8.7
          6. tt0073486	One Flew Over the Cuckoo's Nest	1976-11-18	Drama	NULL	USA	R	Milos Forman	108997629	891071	8.7*/

Fill the NULLS in the duration column

        UPDATE DataCleaning..movie_data
        SET duration = '154'
        WHERE duration IS NULL AND imdb_id = 'tt0110912';


        UPDATE DataCleaning..movie_data
        SET duration = '195'
        WHERE duration IS NULL AND imdb_id = 'tt0108052';


        UPDATE DataCleaning..movie_data
        SET duration = '179'
        WHERE duration IS NULL AND imdb_id = 'tt0137523';


        UPDATE DataCleaning..movie_data
        SET duration = '136'
        WHERE duration IS NULL AND imdb_id = 'tt0133093';


        UPDATE DataCleaning..movie_data
        SET duration = '124'
        WHERE duration IS NULL AND imdb_id = 'tt0080684';


        UPDATE DataCleaning..movie_data
        SET duration = '133'
        WHERE duration IS NULL AND imdb_id = 'tt0073486';
        
        SELECT * FROM DataCleaning..movie_data
        
After running the SELECT function, I copied all the data from movie_data into a blank Excel worksheet for final cleaning and renamed it final_movie_data.

### 4. Excel
These are the actions taken:
- I converted release_year into date format, MM/DD/YYYY: Ctrl + 1 - Number - Date -Type
- I aligned all data in the sheet into Center across selection: Ctrl + 1 - Alignment - Text alignment
- I converted the income column into currency: Ctrl + 1 - Number - Currency
- I separated 000s by comma in the votes column: Ctrl + 1 - Number

#### Disclaimer

This project was of a small dataset but highlights the basics of data cleaning using SQL and Excel. 
Following the steps highlighted in this document guarantee you to get similar results as mine.
However, some steps like use of ChatGPT to clean the data might not be ideal for a large dataset.

