# Section I SQL Syntax

## Overview

In this section, we will look at the logical representation and data processing using SQL. You can get your hands dirty by manipulating the database on [A Tour of TiDB](https://tour.tidb.io/).

If you are quite familiar with SQL, you may skip this section.

## An Overview of SQL Syntax

In relational databases, data exists in the form of `tables`. For example, in [A Tour of TiDB](https://tour.pingcap.io/), there is a `person` table:

|number|name|region|birthday|
|------:|----:|------:|--------:|
|1|tom|north|2019-10-27|
|2|bob|west|2018-10-27|
|3|jay|north|2018-10-25|
|4|jerry|north|2018-10-23|

The table has four rows, and within each row, there are four columns. Using one or more of these tables, we can perform various operations on it to get the information we need.

The easiest operation on a table is to output all rows, for example:

```sql
TiDB> select * from person;
+--------+-------+--------+------------+
| number | name  | region | birthday   |
+--------+-------+--------+------------+
| 1      | tom   | north  | 2019-10-27 |
| 2      | bob   | west   | 2018-10-27 |
| 3      | jay   | north  | 2018-10-25 |
| 4      | jerry | north  | 2018-10-23 |
+--------+-------+--------+------------+
4 row in set (0.00 sec)
```

You can also specify to output only the columns you need, for example:

```sql
TiDB> select number, name from person;
+--------+-------+
| number | name  |
+--------+-------+
| 1      | tom   |
| 2      | bob   |
| 3      | jay   |
| 4      | jerry |
+--------+-------+
4 row in set (0.01 sec)
```

Of course, we may be interested in rows that satisfy certain conditions. For example,we may only care about people located in the `north` region:

```sql
TiDB> select name, birthday from person where region = 'north';
+-------+------------+
| name  | birthday   | 
+-------+------------+
| tom   | 2019-10-27 | 
| jay   | 2018-10-25 | 
| jerry | 2018-10-23 | 
+-------+------------+
3 row in set (0.01 sec)
```

The example above uses `where` clause to filter out records fulfill a specified condition.

Sometimes, we need summarized data. For instance, we want to find out the number of rows in the table that meet a certain condition. At this time, we need `count(*)`, an aggregate function, to count the summarized information:

```sql
TiDB> select count(*) from person where region = 'north';
+----------+
| count(*) | 
+----------+
| 3        | 
+----------+
1 row in set (0.01 sec)
```

Common aggregate functions include `max()`, `min()`, `sum()`, and `count()`. The query above outputs the number of lines that satisfy `region = 'north'`. What if we also wanted to know the total number of people in all other regions? This is where the `group by` comes in handy:

```sql
TiDB> select region, count(*) from person group by region;
+--------+----------+
| region | count(*) |
+--------+----------+
| north  | 3        |
| west   | 1        |
+--------+----------+
2 row in set (0.01 sec)
```

Of course, we may still need to filter some rows from the aggregated results. In this case, we can use `having` clauses because the `where` keyword cannot be used with aggregate functions.

```
TiDB> select region, count(*) from person group by region having count(*) > 1;
+--------+----------+
| region | count(*) | 
+--------+----------+
| north  | 3        | 
+--------+----------+
1 row in set (0.02 sec)
```

Also, there are some other common operations such as `order by` and `limit`.  

Apart from operations on a single table, we often need to combine information from multiple tables. For example, there is another `address` table:

```sql
TiDB> create table address(number int, address varchar(50));
Execute success (0.05 sec)
TiDB> insert into address values (1, 'a'),(2, 'b'),(3, 'c'), (4, 'd');
Execute success (0.02 sec)
```

The easiest way to combine data from the two tables is to take any row from the two tables and combine them separately. As a result, we will have a total of 4 * 4 = 16 combinations.

```sql
TiDB> select name, address from person inner join address;
+-------+---------+
| name  | address |
+-------+---------+
| tom   | a       |
| tom   | b       |
| tom   | c       |
| tom   | d       |
| bob   | a       |
| bob   | b       |
| bob   | c       |
| bob   | d       |
| jay   | a       |
| jay   | b       |
| jay   | c       |
| jay   | d       |
| jerry | a       |
| jerry | b       |
| jerry | c       |
| jerry | d       |
+-------+---------+
16 row in set (0.02 sec)
```

However, the information explosion generated by this kind of `join` is often unnecessary. We combine rows from two or more tables, based on a related column between them.

```sql
TiDB> select name, address from person inner join address on person.number = address.number;
+-------+---------+
| name  | address |
+-------+---------+
| tom   | a       |
| bob   | b       |
| jay   | c       |
| jerry | d       |
+-------+---------+
4 row in set (0.02 sec)
```

There are also some other types of `join`, including `left join` and `right join`.


## Homework

Complete the following HackerRank questions

- [Revising the Select Query I](https://www.hackerrank.com/challenges/revising-the-select-query/problem) (10%)
- [Revising the Select Query II](https://www.hackerrank.com/challenges/revising-the-select-query-2/problem) (10%)
- [Revising Aggregations The Count Function](https://www.hackerrank.com/challenges/revising-aggregations-the-count-function/problem) (10%)
- [Revising Aggregations The Sum Function](https://www.hackerrank.com/challenges/revising-aggregations-sum/problem) (10%)
- [Revising Aggregations Revision](https://www.hackerrank.com/challenges/revising-aggregations-the-average-function/problem) (10%)
- [African Cities](https://www.hackerrank.com/challenges/african-cities/problem) (10%)
- [Average Population of Each Population](https://www.hackerrank.com/challenges/average-population-of-each-continent/problem) (10%)
- [Binary Tree Nodes](https://www.hackerrank.com/challenges/binary-search-tree-1/problem) (30%)

## Grading

This homework is not graded.