
# SQL


DATE:  04-01-25


Tags:

# References:




# Content:
- ![[Attachments/Pasted image 20250108142442.png]]
- ![[Attachments/Screen+Shot+2016-04-17+at+12.22.49+PM.png]]
- ### QUERYING:
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
	- `GROUPBY` - ![[Attachments/Pasted image 20250117195727.png]]![[Attachments/Pasted image 20250117221732.png]]![[Attachments/Pasted image 20250117201602.png]]![[Attachments/Pasted image 20250120175854.png]]![[Attachments/Pasted image 20250120175935.png]]
	- `HAVING` - Allows us to filter after the AGGREGATION has already taken place using `GROUP BY`![[Attachments/Pasted image 20250120181620.png]]
	- AS - column alias
		- ![[Attachments/Pasted image 20250121140345.png]]Only get created at the very end of the data output.
		- ![[Attachments/Pasted image 20250121140805.png]]
	- JOINS:
		- INNER:  
			- ![[Attachments/Pasted image 20250121142232.png]]
			- ![[Attachments/Pasted image 20250121142829.png]]
			- ![[Attachments/Pasted image 20250123133208.png]]
		- OUTER:
			- ![[Attachments/Pasted image 20250121143021.png]]![[Attachments/Pasted image 20250121143113.png]]
			- ![[Attachments/Pasted image 20250121143249.png]]![[Attachments/Pasted image 20250121143707.png]]
		- LEFT JOIN:
			- ![[Attachments/Pasted image 20250122135840.png]]![[Attachments/Pasted image 20250122135957.png]]
			- ![[Attachments/Pasted image 20250122141256.png]]![[Attachments/Pasted image 20250122142246.png]]
	- UNION:![[Attachments/Pasted image 20250123131748.png]]
		- ![[Attachments/Pasted image 20250123131919.png]]![[Attachments/Pasted image 20250123132208.png]]
	- Time:![[Attachments/Pasted image 20250123182415.png]]
		- ![[Attachments/Pasted image 20250123182544.png]]SELECT TIMEZONE / NOW() / TIMEOFDAY() / CURRENT_TIME / CURRENT_DATE
		- ![[Attachments/Pasted image 20250123183239.png]]![[Attachments/Pasted image 20250123183322.png]]
		- ![[Attachments/Pasted image 20250123183334.png]]
		- ![[Attachments/Pasted image 20250123183349.png]]![[Attachments/Pasted image 20250123183502.png]]
	- Mathematical Operations:
		- ![[Attachments/Pasted image 20250123184345.png]]
	- String Opeartions:
		- ![[Attachments/Pasted image 20250123184515.png]]
		- ![[Attachments/Pasted image 20250123231212.png]]![[Attachments/Pasted image 20250123231335.png]]
	- SUBQUERY:![[Attachments/Pasted image 20250123231939.png]]![[Attachments/Pasted image 20250123232202.png]]
		- ![[Attachments/Pasted image 20250123232123.png]]
		- ![[Attachments/Pasted image 20250123232219.png]]![[Attachments/Pasted image 20250123233541.png]]
	- EXISTS:![[Attachments/Pasted image 20250123232515.png]]
		- ![[Attachments/Pasted image 20250123233912.png]]
	- SELF JOIN: ![[Attachments/Pasted image 20250123234023.png]]![[Attachments/Pasted image 20250123234053.png]]
		- ![[Attachments/Pasted image 20250123234752.png]]


Sequence:   `FROM -> JOIN -> WHERE(comparison OP, (NOT) BETWEEN, (NOT) IN, LIKE, ILIKE, logical op) -> GROUP BY -> HAVING -> ORDER BY -> LIMIT -> SELECT -> AS`


## CREATING TABLE:


- Data Types:![[Attachments/Pasted image 20250124004642.png]]
- ![[Attachments/Pasted image 20250124004717.png]]
- PRIMARY KEYS:![[Attachments/Pasted image 20250124004902.png]]
- FOREIGN KEYS:![[Attachments/Pasted image 20250124005002.png]]
- ![[Attachments/Pasted image 20250124005234.png]]
- CONSTRAINTS:![[Attachments/Pasted image 20250124005417.png]]![[Attachments/Pasted image 20250124005500.png]]
	-   ![[Attachments/Pasted image 20250124005510.png]]![[Attachments/Pasted image 20250124005532.png]]![[Attachments/Pasted image 20250124005546.png]]![[Attachments/Pasted image 20250124005603.png]]
	- ![[Attachments/Pasted image 20250124005709.png]]![[Attachments/Pasted image 20250124180018.png]]![[Attachments/Pasted image 20250124005744.png]]

- General Syntax For CREATE TABLE:![[Attachments/Pasted image 20250124172234.png]]
	- Implementing foreign_key:![[Attachments/Pasted image 20250124173049.png]]

- INSERT Data into Table:![[Attachments/Pasted image 20250124173217.png]]
	- ![[Attachments/Pasted image 20250124173541.png]]

- UPDATE DATA in Table:![[Attachments/Pasted image 20250124174113.png]]![[Attachments/Pasted image 20250124174253.png]]
	- ![[Attachments/Pasted image 20250124174317.png]]

- DELETE DATA in Table:![[Attachments/Pasted image 20250124175040.png]]

- ALTER TABLE schema:![[Attachments/Pasted image 20250124175147.png]]![[Attachments/Pasted image 20250124175228.png]]![[Attachments/Pasted image 20250124175244.png]]![[Attachments/Pasted image 20250124175258.png]]




## Conditional Expressions and Procedures:

- CASE:
	- ![[Attachments/Pasted image 20250124184607.png]]![[Attachments/Pasted image 20250124184702.png]]![[Attachments/Pasted image 20250124185706.png]]
	- ![[Attachments/Pasted image 20250124184859.png]]![[Attachments/Pasted image 20250124184942.png]]![[Attachments/Pasted image 20250124185912.png]]
	- ![[Attachments/Pasted image 20250124190106.png]]![[Attachments/Pasted image 20250124190202.png]]

- COALESCE:![[Attachments/Pasted image 20250124190343.png]]![[Attachments/Pasted image 20250124190850.png]]

- CAST:![[Attachments/Pasted image 20250124190951.png]]![[Attachments/Pasted image 20250124191112.png]]

- NULLIF:![[Attachments/Pasted image 20250124191210.png]]![[Attachments/Pasted image 20250124191218.png]]
	- ![[Attachments/Pasted image 20250124191902.png]]


- VIEWS:![[Attachments/Pasted image 20250124191954.png]]![[Attachments/Pasted image 20250124192049.png]]![[Attachments/Pasted image 20250124192123.png]]
	- ![[Attachments/Pasted image 20250124192330.png]]![[Attachments/Pasted image 20250124192423.png]]
- 