README
======================================================================

First steps to initialize services
------------------------------------
> sudo su -

#Start crossdata
> service crossdata start

#Start Connectors
> service connector_cassandra start
> service connector_sparksql start

#At this point, we have all that we need so now we start crossdata shell
> cd /opt/sds/crossdata/bin
> ./crossdata-sh

Playing with the shell
-----------------------------------
This is an example of crossdata use:

The first steps are to attach the connectors...

1. ADD DATASTORE /etc/sds/connectors/cassandra/CassandraDataStore.xml;
2. ATTACH CLUSTER cassandra_prod ON DATASTORE Cassandra WITH OPTIONS {'Hosts': '[127.0.0.1]', 'Port': 9042, 'rpcPort':9160, 'cluster':'cassandra_prod'};
3. ADD CONNECTOR /etc/sds/connectors/cassandra/CassandraConnector.xml;
4. ADD CONNECTOR /etc/sds/connectors/sparksql/SparkSQLConnector.xml;
5. ATTACH CONNECTOR CassandraConnector TO cassandra_prod WITH OPTIONS {'DefaultLimit': '1000'} AND PRIORITY=1;
6. ATTACH CONNECTOR SparkSQLConnector TO cassandra_prod WITH OPTIONS {'DefaultLimit': '1000'};

Now we can operate as usual...

7. CREATE CATALOG test;
8. USE test;         
9. CREATE TABLE table1 ON CLUSTER cassandra_prod (id int PRIMARY KEY, serial int, name text, rating double, email text);
10. CREATE TABLE table2 ON CLUSTER cassandra_prod (id int PRIMARY KEY, lastname text, age int, company text);

You can insert a few rows by executing:

11. INSERT INTO test.table1(id, serial, name, rating, email) VALUES (999, 54000, 'Peter', 8.9,
'myemail@yahoo.com');
12. INSERT INTO test.table1(id, serial, name, rating, email) VALUES (1000, 71098, 'Charles', 2.7,
'contact@stratio.com');
13. INSERT INTO test.table1(id, serial, name, rating, email) VALUES (1001, 34539, 'John', 9.3,
'crossdata@stratio.com');

14. INSERT INTO test.table2(id, lastname, age, company) VALUES (999, 'Miller', 23, 'Best Company');
15. INSERT INTO test.table2(id, lastname, age, company) VALUES (1000, 'Fernandez', 35, 'Stratio');
16. INSERT INTO test.table2(id, lastname, age, company) VALUES (1001, 'Yorke', 42, 'Big Data Company');


17. USE test;
18. SELECT * FROM test.table1;
19. SELECT count(*) FROM table1;
20. SELECT now() FROM table1;
21. SELECT id, age FROM table2;

22. SELECT name, age FROM table1 INNER JOIN table2 ON table1.id=table2.id;

You can execute a insert from select statement

23. CREATE TABLE table3 ON CLUSTER cassandra_prod (id int PRIMARY KEY, company text);
24. INSERT INTO test.table3(id,company) select table2.id, table2.company from test.table2;
25. SELECT * FROM table3;

Let's create a full text index:

26. CREATE FULL_TEXT INDEX myIndex ON table1(email);

Now we can execute a search query:
27. SELECT * FROM table1 WHERE email MATCH '*yahoo*';

Or you can execute an OR search query:
28. SELECT * FROM table1 where email=should('*yahoo*','*crossdata*');


For more information about grammar, please visit our github site:
https://github.com/Stratio/crossdata/tree/0.3.3-RELEASE

If you find any kind of bug or issue please report to:
https://crossdata.atlassian.net
