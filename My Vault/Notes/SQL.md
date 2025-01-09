
# SQL


DATE:  04-01-25


Tags:

# References:




# Content:
- ![[Attachments/Pasted image 20250108142442.png]]
- ![[Attachments/Screen+Shot+2016-04-17+at+12.22.49+PM.png]]
- ### Fundamentals:
	- `SELECT` - Query columns of a table
	- `DISTINCT` - Returns distinct values of a column
		- `SELECT DISTINCT(column_1) FROM table_1` - Operates on a column
	- `COUNT` - Return the num of input rows that match a specific condition of a query
		- `SELECT COUNT(column_1) FROM table_1` - Operates on a column, returns num of rows in that column. Since all columns have same num of rows. it does not matter which column you use the COUNT on!
		- `SELECT COUNT(DISTINCT (column_1)) FROM table_1`
	- `WHERE` - Allows us to specify conditions on columns for the rows to be returned. Appears immediately after that from clause of the select statement and then the conditions
		- Conditions:
			- comparison: ![[Attachments/Pasted image 20250109205713.png]]
			- logical: ![[Attachments/Pasted image 20250109205745.png]]
		- ```SELECT name, age FROM table
		  WHERE name='Rahul' AND age>20;```
		- ```SELECT title FROM films
		  WHERE rated='R' AND rating>8.5 AND director='Nolan';``` - Sequence is FROM->WHERE->SELECT
		- 
	