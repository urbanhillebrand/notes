# MySQL

## Basics

***Anzeige***

```
SHOW DATABASES;
DESCRIBE mysql.user;
SELECT User, Host FROM mysql.user;
\s ①
```
1. Status anzeigen

***User und DB anlegen***

```
CREATE USER 'nextcloud_eca'@'localhost' IDENTIFIED BY 'abcde';
CREATE DATABASE nextcloud_eca;
GRANT ALL PRIVILEGES ON nextcloud_eca.* TO 'nextcloud_eca'@'localhost';
```

***User und DB löschen***

```
DROP DATABASE dbname
DROP USER username
```

***DB-Dump***

```
# create:
mysqldump -uUSER -p DBNAME > DBNAME.sql # fragt nach PW
# oder
mysqldump -uUSER -pPASSWORD DBNAME > DBNAME.sql # PW auf cmdline

# restore:
mysql -uUSER -p DBNAME &lt; DBNAME.sql
```

## Berechtigungen

* ALL PRIVILEGES
* CREATE
* DROP
* DELETE
* INSERT
* SELECT
* UPDATE
* GRANT OPTION

***Selektive Berechtigungen***

```
GRANT type_of_permission ON database_name.table_name TO 'username'@'localhost'
REVOKE ... ①
SHOW GRANTS username
FLUSH PRIVILEGES
```
1. Gleiche Syntax wie GRANT

## Weitere Snippets

***Datenbankgrößen***

```
SELECT table_schema AS "Database",
ROUND(SUM(data_length + index_length) / 1024 / 1024, 2) AS "Size (MB)"
FROM information_schema.TABLES
GROUP BY table_schema;
```

***Tabellengrößen***

```
SELECT table_name AS "Table",
ROUND(((data_length + index_length) / 1024 / 1024), 2) AS "Size (MB)"
FROM information_schema.TABLES
WHERE table_schema = "database_name"
ORDER BY (data_length + index_length) DESC;
```

***row format ändern***

```
#!/bin/bash

# Prompt for database credentials
read -p "Enter Database Name: " DB_NAME
read -p "Enter Username: " DB_USER
read -s -p "Enter Password: " DB_PASS
echo

# Execute all ALTER TABLE statements in one query
mysql -u "$DB_USER" -p"$DB_PASS" "$DB_NAME" -Bse "
SELECT CONCAT('ALTER TABLE \’, TABLE_NAME, '\` ROW_FORMAT=DYNAMIC;')
FROM INFORMATION_SCHEMA.TABLES
WHERE TABLE_SCHEMA = '$DB_NAME'
AND ENGINE = 'InnoDB'
" | mysql -u "$DB_USER" -p"$DB_PASS" "$DB_NAME"
```
