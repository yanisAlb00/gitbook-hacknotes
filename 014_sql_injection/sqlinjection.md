# Types of Databases

Relational Databases : A relational database is the most common type of database. It uses a schema, a template, to dictate the data structure stored in the database and a concept is required to link one table to another using its key, called a relational database management system (RDBMS).

The most common example of relational databases is MySQL

Non-relational Databases : A non-relational database (also called a NoSQL database) does not use tables, rows, and columns or prime keys, relationships, or schemas. Instead, a NoSQL database stores data using various storage models, depending on the type of data stored.

The most common example of a NoSQL database is MongoDB.







DESCRIBE employees;
-> 6 colonnes
DESCRIBE departments;
-> 2 colonnes

SELECT * from employees UNION SELECT dept_no, dept_name, 2, 3, 4, 5 from departments;

