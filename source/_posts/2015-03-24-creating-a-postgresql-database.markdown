---
layout: post
title: "Creating a PostgreSQL Database"
date: 2015-03-24 11:08:59 -0500
comments: true
categories:
---

[PostgreSQL](http://www.postgresql.org/) is an open-source object-relational database management system. PostgreSQL is derived from a project completed at UC Berkeley, and it is now the most advanced open-source database available.
<!--more-->

### Getting Started

To begin working with PostgreSQL, you will need to download it [here](http://www.postgresql.org/download/). Conceptually, it helps to think of a database as an Excel file. A database is comprised of tables and the tables are comprised of columns. To create a database in the terminal, type: `createdb database_name`.


In order to start developing the database's schema (i.e. its configuration), type: `psql database_name`.

To create a table within the database, type: `CREATE TABLE table_name ();`.

Typing `\d` will display a list of the database's relations. To illustrate, consider a database with the name "example" that has a table with the name "test." Typing `\d` will return the following:

```
 List of relations
 Schema | Name | Type  |  Owner
--------+------+-------+----------
 public | test | table | lisahamm
(1 row)
```

### Working with tables

Once you have a database with a table, the next logical step would be to add a column. To do so, type: `alter table table_name add column column_name data_type;`. For instance, lets say I want to add a column to store names in my "test" table created above. This would be accomplished by the following code:

```
alter table test add column name text;
```
Likewise, adding a second column to the "test" table to store email addresses would be accomplished by the following code:

```
alter table test add column email text;
```

Running `\d <table_name>` will display the table's columns. Executing this for the "test" table would look like:

```
example=# \d test;
    Table "public.test"
 Column | Type | Modifiers
--------+------+-----------
 name   | text |
 email  | text |
```

In order to get the data for all columns, you can use:

```
select * from table_name
```
Running this on the "test" table is not too exciting at the moment since data has yet to be entered:

```
example=# select * from test;
 name | email
------+-------
(0 rows)
```

### Adding data values to a table

To add data values to a table, type:

```
insert into table_name (column_name) VALUES('data value');
```
Lets add some data to the "test" table for someone by the name of "Bob" with an email address of "bob@test.com":

```
insert into test (name, email) VALUES('Bob', 'bob@test.com');
```

There is now data in the "test" table that will be displayed by `select *`:

```
example=# select * from test;
 name |       email
------+--------------------
 Bob  | bob@test.com
(1 row)
```

### Selecting specific data, or, querying a table

Generally, tables will contain more than one row of data. Consider our test table with the following data rows in it:

```
example=# select * from test;
 name |       email
------+--------------------
 Bob  | bob@test.com
 Bob  | bobby@example.com
 Lisa | lisa@test.com
 Katie| katie@test.com
(3 rows)
```
How do we select specific data? This is also referred to as 'querying a table' and can be done by adding some additional code to the SQL `select` statement. For instance, this is how you would select all data rows containing a name equal to "Bob":

```
example=# select * from test where name='Bob';
 name |       email
------+-------------------
 Bob  | bob@test.com
 Bob  | bobby@example.com
(2 rows)
```

To select just email addresses for those with a name of "Bob":

```
example=# select email from test where name='Bob';
       email
-------------------
 bob@test.com
 bobby@example.com
(2 rows)
```

To select email and name, respectively, for those named "Bob":

```
example=# select email,name from test where name='Bob';
       email       | name
-------------------+------
 bob@test.com      | Bob
 bobby@example.com | Bob
(2 rows)
```

Perhaps there is a need for the number of "Bobs" in the table. This is found by using `count`:

```
example=# select count(*) from test where name='Bob';
count
-------
     2
(1 row)
```
It is also possible to limit results to a specific number:

```
example=# select email,name from test where name='Bob' limit 1;
       email       | name
-------------------+------
 bob@test.com      | Bob
(1 row)
```
Perhaps there is a need for all names to be ordered alphabetically. This can be done with the following line of code:

```
select name from test order by name ASC;
```

Note, "ASC" can be replaced with "DESC" to return data in descending order.


### Conclusion

Thinking of databases as Excel files may help in understanding them conceptually. Knowing specifics relating to querying tables is important because PostgreSQL is well-equipped for doing this efficiently. For this reason, ordering, searching, and other queries should be done by the database and not somewhere else.



