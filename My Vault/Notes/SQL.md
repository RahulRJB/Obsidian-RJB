
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
		  WHERE rated='R' AND rating>8.5 AND director='Nolan';``` 
	- `ORDER BY` - Sort rows by column value
		-  ```SELECT * FROM customer_db
		  WHERE city='Kolkata'
		  ORDER BY cust_id ASC, name DESC
		  ```
	- `LIMIT` - Limits the num of rows returned by the query
		-  ```SELECT * FROM customer_db
		  WHERE city='Kolkata' and amount!=0.0
		  ORDER BY payment_date DESC
		  LIMIT 5
		  ``` -  5 most recent payments done in Kolkata
	- `BETWEEN` - Tag along WHERE statement![[Attachments/Pasted image 20250117185402.png]]![[Attachments/Pasted image 20250117185441.png]]
		- ![[Attachments/Pasted image 20250117185535.png]]
		- ```SELECT * FROM payments_db
		  WHERE amount BETWEEN 8 AND 9
		  ORDER BY amount DESC```
	- `IN` - ```SELECT * FROM payments_db
		  WHERE name IN ('Rahul', 'Ruchira')```
	- ![[Attachments/Pasted image 20250117194640.png]]![[Attachments/Pasted image 20250117194702.png]]![[Attachments/Pasted image 20250117194808.png]]


- Aggregate Function: ![[Attachments/Pasted image 20250117200124.png]]
	- Only happens in `SELECT` or `HAVING` clause.
	- Returns only a number
	- `SELECT COUNT(amount), ROUND(AVG(amount), 2), MAX(amount), MIN(amount), SUM(...) FROM transaction`
- 





- `GROUPBY` - ![[Attachments/Pasted image 20250117195727.png]]![[Attachments/Pasted image 20250117201602.png]]
	-  ```SELECT company, avg(price) FROM prads_db
	  GROUP BY company```





Sequence is FROM -> WHERE(comparison OP, (NOT) BETWEEN, (NOT) IN, LIKE, ILIKE, logical op) -> ORDER BY -> LIMIT -> SELECT