# whmcs-cluster-convertor
Convert your production WHMCS database to use InnoDB and have PRIMARY KEYS

Eventually will be a PHP Script

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


Find all tables without a PRIMARY KEY.
```
SELECT tab.table_schema AS database_name,
       tab.table_name
FROM information_schema.tables tab
LEFT JOIN information_schema.table_constraints tco
          ON tab.table_schema = tco.table_schema
          AND tab.table_name = tco.table_name
          AND tco.constraint_type = 'PRIMARY KEY'
WHERE tco.constraint_type IS NULL
      AND tab.table_schema NOT IN('mysql', 'information_schema', 
                                  'performance_schema', 'sys')
      AND tab.table_type = 'BASE TABLE'
ORDER BY tab.table_schema,
         tab.table_name;
```
Source: https://dataedo.com/kb/query/mysql/find-tables-without-primary-keys


Example to add a virtual primary key.
```
ALTER TABLE `your_database`.`mod_invoicedata`   
  ADD COLUMN `virtual_pk` INT(11) NOT NULL AFTER `customfields`, 
  ADD PRIMARY KEY (`virtual_pk`);
```
