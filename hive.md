su -
pass: cloudera

-- Step 0: Create a file
gedit /home/cloudera/Desktop/stud

101,Alice,8.5
102,Bob,7.9
103,Charlie,9.1

Type hive and press enter

-- Create database
CREATE DATABASE databasename;

-- Use the newly created database
USE databasename;

-- Create table with '|' as field delimiter
CREATE TABLE tablename (id INT, name STRING, gpa FLOAT) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',';

-- Load data from local file (created at /home/cloudera/Desktop/stud)
LOAD DATA LOCAL INPATH '/home/cloudera/Desktop/stud'
INTO TABLE tablename;

-- View all databases
SHOW DATABASES;

-- Show all tables in current database
SHOW TABLES;

-- Select all data from table
SELECT * FROM tablename;

-- Insert a single row into the table
INSERT INTO TABLE tablename VALUES (104, 'David', 8.2);

