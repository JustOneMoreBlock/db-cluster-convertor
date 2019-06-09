Select and change MyISAM to InnoDB
```
SET @DATABASE_NAME = 'Your WHMCS db';

SELECT  CONCAT('ALTER TABLE `', table_name, '` ENGINE=InnoDB;') AS sql_statements
FROM    information_schema.tables AS tb
WHERE   table_schema = @DATABASE_NAME
AND     `ENGINE` = 'MyISAM'
AND     `TABLE_TYPE` = 'BASE TABLE'
ORDER BY table_name DESC;
```
Source: https://stackoverflow.com/a/9492183/1926449