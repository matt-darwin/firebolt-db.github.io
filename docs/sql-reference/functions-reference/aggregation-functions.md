---
layout: default
title: Aggregation functions
nav_order: 1
parent: SQL functions reference
---

# Aggregation functions
{: .no_toc}

* Topic ToC
{:toc}

## ANY

Synonym for [ANY_VALUE](#any-value).

## ANY\_VALUE

Returns a single arbitrary value from the specified column. This function ignores `NULL`s, so the only time it will return `NULL` is when all inputs are `NULL`s.

##### Syntax
{: .no_toc}


```SQL
​​ANY_VALUE(<col>)​​
```

| Argument | Description                                  | Data Type |
| :-------- | :-------------------------------------------- | :--------- |
| `<col>`  | The column from which the value is returned. | Any       |

##### Return Type
{: .no_toc}

Same as input argument

##### Example
{: .no_toc}

Consider a table, `example_table`, with a single column `first_name` as shown below.

```
+------------+
| first_name |
+------------+
| Sammy      |
| NULL       |
| Carol      |
| Lei        |
| Mickey     |
+------------+
```

The first time the query below runs, `Carol` might be returned. The second time the query runs, `Carol` or any other value, such as `Lei` or `Sammy`, might be returned, but `NULL` will never be returned.

```sql
SELECT
	ANY_VALUE(first_name)
FROM
	example_table;
```

## APPROX\_PERCENTILE

Returns an approximate value for the specified percentile based on the range of numbers returned by the expression.&#x20;

For example, if you run `APPROX_PERCENTILE` with a specified `<percent>` of .75 on a column with 2,000 numbers, and the function returned `655`, then this would indicate that 75% of the 2,000 numbers in the column are less than 655.&#x20;

The number returned is not necessarily in the original range of numbers.

##### Syntax
{: .no_toc}

```sql
APPROX_PERCENTILE(<expr>,<percent>)
```

| Parameter   | Description                                                                                                               |
| :----------- | :------------------------------------------------------------------------------------------------------------------------- |
| `<expr>`    | A valid expression, such as a column name, that evaluates to numeric values.                                              |
| `<percent>` | A constant real number greater than or equal to 0.0 and less than 1. For example, `.999` specifies the 99.9th percentile. |

##### Example
{: .no_toc}

To demonstrate `APPROX_PERCENTILE`, we'll use the example table `number_test` as created below. This provides a range of numbers between 1 and 100.

```sql
CREATE DIMENSION TABLE IF NOT EXISTS number_test
	(
		First_name TEXT
	);

INSERT INTO
	number_test
VALUES
	(1),
	(100),
	(55),
	(16),
	(48),
	(86),
	(33),
	(22);
```

The example below shows `APPROX_PERCENTILE` of 50% of the number range in `number_test`.&#x20;

```sql
SELECT
	APPROX_PERCENTILE(num, 0.5)
FROM
	number_test;
```

**Returns**: `40.5`

The example below shows an `APPROX_PERCENTILE` of 25%.&#x20;

```sql
SELECT
	APPROX_PERCENTILE(num, 0.25)
FROM
	number_test;
```

**Returns**: `20.5`

## AVG

Calculates the average of an expression.

##### Syntax
{: .no_toc}

```sql
​​AVG(<expr>)​​
```

| Parameter | Description                                                                                                                                                                        |
| :--------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `<expr>`  | The expression used to calculate the average. Valid values for the expression include column names or functions that return a column name for columns that contain numeric values. |

{: .note}
The `AVG()` aggregation function ignores rows with NULL. For example, an `AVG` from 3 rows containing `1`, `2`, and NULL returns `1.5` because the NULL row is not counted. To calculate an average that includes NULL, use `SUM(COLUMN)/COUNT(*)`.


## CHECKSUM

Calculates a hash value known as a checksum operation on a list of arguments. When `*` is specified as an argument - calculates checksum over all columns in the input. Performing a checksum operation is useful for warming up table data or to check if the same values exist in two different tables.

##### Syntax
{: .no_toc}

```sql
CHECKSUM( <expr1> [, <expr2>] [, <expr3>] [, ...<exprN>] )
CHECKSUM(*)
```

##### Example
{: .no_toc}

For this example, we'll create a new table `albums` as shown below.&#x20;

```sql
CREATE DIMENSION TABLE albums (year INT, artist TEXT, title TEXT);

INSERT INTO
	albums
VALUES
	(1982, 'Michael Jackson', 'Thriller'),
	(1973, 'Pink Floyd', 'The Dark Side of the Moon'),
	(1969, 'The Beatles', 'Abbey Road'),
	(1976, 'Eagles', 'Hotel California');
```

The example below calculates a checksum based on all columns in the table `albums`.

```sql
SELECT CHECKSUM(*) FROM albums;
```

**Returns**: `1630068993470241196`

The next example calculates a checksum based on columns `year` and `title` only.

```sql
SELECT CHECKSUM(year, title) FROM albums;
```

**Returns**: `4056287705143712538`

## COUNT

Counts the number of rows or not NULL values.

##### Syntax
{: .no_toc}

```sql
COUNT([ DISTINCT ] <expr>)
```

| Parameter | Description                                                                                                                                                                                                           |
| :--------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `<expr>`  | Valid values for the expression include column names (or \* for counting all columns) or functions that return a column name. When `DISTINCT` is being used, counts only the unique number of rows with no `NULL` values. |

{: .note}
> `COUNT(*)` returns a total count of all rows in the table, while `COUNT(<column_name>)` returns a count of non-NULL rows in the specified `<column_name>`.
>
> By default, `COUNT(DISTINCT)` returns approximate results. To get a precise result, with a performance penalty, use `SET firebolt_optimization_enable_exact_count_distinct=1;`

##### Example
{: .no_toc}

For this example, we'll create a new table `number_test` as shown below.&#x20;

```sql
CREATE DIMENSION TABLE IF NOT EXISTS number_test
	(
		num TEXT
	);

INSERT INTO
	number_test
VALUES
	(1),
	(1),
	(2),
	(3),
	(3),
	(3),
	(4),
	(5);
```

Doing a regular `COUNT` returns the total number of rows in the column. We inserted 8 rows earlier, so it should return the same number.

```sql
SELECT
	COUNT(num)
FROM
	number_test;
```

**Returns**: `8`

A `COUNT(DISTINCT)` function on the same column returns the number of unique rows. There are five unique numbers that we inserted earlier.&#x20;

```sql
SELECT
	COUNT(DISTINCT num)
FROM
	number_test;
```

**Returns**: `5`

## MAX

Calculates the maximum value of an expression across all input values.

##### Syntax
{: .no_toc}

```sql
​​MAX(<expr>)
```

| Parameter | Description                                                                                                                                        |
| :--------- | :-------------------------------------------------------------------------------------------------------------------------------------------------- |
| `<expr>`  | The expression used to calculate the maximum values. Valid values for the expression include a column name or functions that return a column name. |

##### Example
{: .no_toc}

For this example, we'll create a new table `prices` as shown below.

```sql
CREATE DIMENSION TABLE IF NOT EXISTS prices
    (
        item TEXT,
        price INT
    );

INSERT INTO
	prices
VALUES
	('apple', 4),
	('banana', 25),
	('orange', 11),
	('kiwi', 20)
```

When used on the num column, `MAX` will return the largest value.

```sql
SELECT
	MAX(price)
FROM
	prices;
```

**Returns**: `25`

MAX can also work on text columns by returning the text row with the characters that are last in the lexicographic order.&#x20;

```
SELECT
	MAX(item)
FROM
	prices;
```

**Returns:** `orange`

## MAX\_BY

The `MAX_BY` function returns a value for the `<arg>` column based on the max value in a separate column, specified by `<val>`.

If there is more than one max value in `<val>`, then the first will be used.

##### Syntax
{: .no_toc}

```sql
MAX_BY(<arg>, <val>)
```

| Parameter | Description                                    |
| :--------- | :---------------------------------------------- |
| `<arg>`   | The column from which the value is returned.   |
| `<val>`   | The column that is search for a maximum value. |

##### Example
{: .no_toc}

For this example, we will again use the `prices` table that was created above for the `MAX` function. The values for that table are below:

| item   | price |
| :------ | :----- |
| apple  | 4     |
| banana | 25    |
| orange | 11    |
| kiwi   | 20    |

In this example below, `MAX_BY` is used to find the item with the largest price.&#x20;

```sql
SELECT
	MAX_BY(item, price)
FROM
	prices;
```

**Returns**: `banana`

## MEDIAN

Calculates an approximate median for a given column.

##### Syntax
{: .no_toc}

```sql
​​MEDIAN(<col>)​​
```

| Parameter | Description                                                                                                        |
| :--------- | :------------------------------------------------------------------------------------------------------------------ |
| `<col>`   | The column used to calculate the median value. This column can consist of numeric data types or DATE and DATETIME. |

##### Example
{: .no_toc}

For this example, we'll create a new table `num_test `as shown below:

```sql
CREATE DIMENSION TABLE IF NOT EXISTS num_test
	(
		num int
	);

INSERT INTO
	num_test
VALUES
	(1),
	(7),
	(12),
	(30),
	(59),
	(76),
	(100);
```

`MEDIAN` returns the approximate middle value between the lower and higher halves of the values.

```sql
SELECT
	MEDIAN(num)
FROM
	number_test
```

**Returns**: `30`

## MIN

Calculates the minimum value of an expression across all input values.

##### Syntax
{: .no_toc}

```sql
​​MIN(<expr>)
```

| Parameter | Description                                                                                                                                        |
| :--------- | :-------------------------------------------------------------------------------------------------------------------------------------------------- |
| `<expr>`  | The expression used to calculate the minimum values. Valid values for the expression include a column name or functions that return a column name. |

##### Example
{: .no_toc}

For this example, we'll create a new table `prices` as shown below.

```sql
CREATE DIMENSION TABLE IF NOT EXISTS prices
    (
        item TEXT,
        price INT
    );

INSERT INTO
	prices
VALUES
	('apple', 4),
	('banana', 25),
	('orange', 11),
	('kiwi', 20);
```

When used on the `num` column, `MIN` will return the largest value.

```sql
SELECT
	MIN(price)
FROM
	prices;
```

**Returns**: `4`

`MIN` can also work on text columns by returning the text row with the characters that are first in the lexicographic order.

```sql
SELECT
	MIN(item)
FROM
	prices;
```

**Returns**: `apple`

## MIN\_BY

The `MIN_BY` function returns the value of `arg` column at the row in which the `val` column is minimal.

If there is more than one minimal values in `val`, then the first will be used.

##### Syntax
{: .no_toc}

```sql
MIN_BY(arg, val)
```

| Parameter | Description                                    |
| :--------- | :---------------------------------------------- |
| `<arg>`   | The column from which the value is returned.   |
| `<val>`   | The column that is search for a minimum value. |

##### Example
{: .no_toc}

For this example, we will again use the `prices` table that was created above for the `MIN` function. The values for that table are below:&#x20;

| item   | price |
| :------ | :----- |
| apple  | 4     |
| banana | 25    |
| orange | 11    |
| kiwi   | 20    |

In this example below, `MIN_BY` is used to find the item with the lowest price.

```sql
SELECT
	MIN_BY(item, price)
FROM
	prices
```

**Returns:** `apple`

## NEST

Takes a column as an argument, and returns an array of the values.

See the [full description](semi-structured-functions/array-functions.md#nest) under Semi-structured data functions.

## STDDEV\_SAMP

Computes the standard deviation of a sample consisting of a numeric expression.

##### Syntax
{: .no_toc}

```sql
STDDEV_SAMP(<expr>)​
```

| Parameter | Description                                                                                |
| :--------- | :------------------------------------------------------------------------------------------ |
| `<expr>`  | Any column with numeric values or an expression that returns a column with numeric values. |

##### Example
{: .no_toc}

For this example, we'll create a new table `num_test `as shown below:

```
CREATE DIMENSION TABLE IF NOT EXISTS num_test (num INT);


INSERT INTO
	num_test
VALUES
	(1),
	(7),
	(12),
	(30),
	(59),
	(76),
	(100);
```

`STDDEV_SAMP` returns the standard deviation for the values.

```
SELECT
	STDDEV_SAMP(num)
FROM
	num_test
```

**Returns**: `38.18251906180054`

## SUM

Calculates the sum of an expression.

##### Syntax
{: .no_toc}

```sql
​​SUM(<expr>)​​
```

| Parameter | Description                                                                                                                              |
| :--------- | :---------------------------------------------------------------------------------------------------------------------------------------- |
| `<expr>`  | The expression used to calculate the sum. Valid values for `<expr>` include column names or expressions that evaluate to numeric values. |

**Example**

For this example, we'll create a new table `num_test `as shown below:

```
CREATE DIMENSION TABLE IF NOT EXISTS num_test (num INT);


INSERT INTO
	num_test
VALUES
	(1),
	(7),
	(12),
	(30),
	(59),
	(76),
	(100);
```

`SUM` adds together all of the values in the `num` column.

```
SELECT
	SUM(num)
FROM
	numb_test
```

**Returns**: `285`
