---
## SQL Joins and Window Functions

When retrieving data from a database, there are specific conditions or criteria you may want to use to analyze that data. You might need to perform calculations, filter out relevant information, group data for specific analysis, or display aggregated results. SQL allows you to perform these operations on single or multiple tables by use of built-in functions. This article focuses on Joins and Window Functions.

---

### JOINS

Joins are used in querying related data that is stored in multiple tables. The tables, though different and containing specific parts of information, are related by having a common matching column that is used to relate them together.

For instance, if you have data in a students table with the fields: name, student_id, residency, year, and a fee table with the fields: student_id, fee_paid, balance, you can relate the two using the student_id as a common field between the two.

Different types of joins can be used in SQL, namely:


**Inner Join:** This retrieves data from the database that matches from the two tables and ignores the records that don’t appear in both tables. Given our tables, a sample inner join script would be:

```sql
Select s.name, f.fee_paid
from students s
Inner join fee f
On s.student_id = f.student_id
```

The result of this query will be all the students who have paid the fee.

![inner-join-venn-diagram](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/0aj35rxbauz7nfjkpgp0.png)


**Left Join:** This retrieves all records that appear on the left table with matching records on the right; if a record appears on the left and not the right, the missing data is replaced with NULL values in the right table. It is important to note that the **order** of the tables matters when writing the script.

A sample script would be:

```sql
Select s.name, f.fee_paid
from students s
Left Join fee f
On s.student_id = f.student_id
```

This retrieves all student names and their corresponding fee payments made; if a student has not paid, the value on the rows from the fee table would be null.

![left-join-venn-diagram](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/tyf3719q02ilei4mjc4x.png)


**Right Join:** It works in a similar way as the Left Join, with the difference being the order of the tables. The results retrieved are from the right table with matching fields on the left and null values for non-matching rows on the left. Sometimes, the use of a right join can be negated by switching the order of the tables and using a Left Join instead.

![right-join-venndiagram](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rcw5fsigyvzf3yx6zvu6.png)


**Full Join:** This join retrieves all records from both tables and fills the unmatched data with nulls from both tables. It returns all records from the two tables and comes in handy when you are inspecting tables for NULL values. In our example:

```sql
Select s.name, f.fee_paid
from students s
Full Join fee f
On s.student_id = f.student_id
```

Returns all records from students and the fee table, and NULL values for missing data from both tables.

![full-join-venn-diagram](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ub605u940d6ddb7q140r.png)


**Cross Join:** This type of join matches data from both tables in combinations. For instance, if you have table A with the fields: R, Y, G and another table with A, B, C, the result will be a combination of all the fields in the first table and the second.

The results will be a combination of (total fields in 1st table) X (total fields in 2nd table).

In our case:

```sql
Select * from table_1
Cross join table_2
```

gives us these results: RA, RB, RC, YA, YB, YC, GA, GB, GC.

![cross-join-venn-diagram](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/c3g0e7usqv3v9s314dp7.png)

---

### Window Functions

These are functions that retrieve data that is aggregated over certain criteria without losing the individual row data. While Group By returns aggregated data in a compressed result depending on the grouping, Window functions maintain the row data and don’t compress the grouped data into a single row.

An example would be:

```sql
Select year, sum(fee)
from students
Group by year
```

This results in a table with collapsed rows and only one row to represent the group and the group total.

![table-with-aggregation-group-by](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/wnzkq8t4aq93t7l2wisw.png)


A window function would look like this:

```sql
Select year, fee, sum(fee) over(partition by year)
from students
```

The window function does not collapse the grouped data; it aggregates the sum and returns it in each row.

![table-with-window-function-aggregation](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/9pm6ur6o464zbmub8stt.png)


The syntax of a window function, therefore, is:

```sql
[function] OVER (expression1, expression2, expression3, …)
```

The ***OVER*** keyword identifies the window over which you want to apply the function. A window is a subset of your data.


#### Functions

This is the first part of a window function. Here, any aggregate function (sum, count, avg, min, max), rank functions (row_number, rank, dense_rank, cume_dist, percent_rank, ntile), and analytic functions (lead, lag, first_value, last_value) can be used.

The second part checks the condition or criteria you want to apply to the view.

Some of the expressions that can be used are:

* **Partition By**: This keyword specifies the dimension or gives the criteria for dividing the data. Example: [function] Over (Partition by year, payment_status ), groups the subset of data into common years and payment status.

* **Order By**: This sorts each subset of data (window) into ascending or descending order. Example: [function] Over (Order by year asc ).

It’s important to note that more expressions or multiple conditions can be used after the ***OVER*** clause to further organize the data in the individual windows.

