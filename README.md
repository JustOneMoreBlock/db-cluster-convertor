# db-cluster-convertor
Convert any database to use InnoDB and have PRIMARY KEYS for sole purpose of master-replication.

The premise is this will find all your tables without `PRIMARY KEYS` automagically `ALTER` your tables to add a `virtual_pk` after column 1 and add a `PRIMARY KEY` that's `NOT NULL` with an `INT(11)` prepares, executes and unsets the variable.

Once completed, the same task will be completed to execute a conversion of your engine from `MyISAM` to `InnoDB` and prepares, executes and unsets the variable.

- Gets tables
- Alters tables
- Converts Engine


IDEAL UNTESTED PROTOTYPE:
```
SET @DATABASE_NAME = 'Your db';
SET @NO_PRIMARY_KEYS = SELECT tab.table_schema AS database_name,
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
         
SET
    @column_name =(
    SELECT COLUMN_NAME
FROM
    information_schema.columns
WHERE
    table_schema = @DATABASE_NAME AND TABLE_NAME = @NO_PRIMARY_KEYS AND ordinal_position = 1
);
SET
    @convert_virual_pk = CONCAT(
         'ALTER TABLE `@DATABASE_NAME`.`@NO_PRIMARY_KEYS`
          ADD COLUMN `virtual_pk` INT(11) NOT NULL AFTER `@column_name`, 
          ADD PRIMARY KEY (`virtual_pk`);
    );
PREPARE
    convert_virual_primary_keys;
FROM
    @convert_virual_pk;
EXECUTE
    convert_virual_primary_keys;
DEALLOCATE
    convert_virual_primary_keys;


SET
    @convert_tables = SELECT  CONCAT('ALTER TABLE `', table_name, '` ENGINE=InnoDB;') AS sql_statements
FROM    information_schema.tables AS tb
WHERE   table_schema = @DATABASE_NAME
AND     `ENGINE` = 'MyISAM'
AND     `TABLE_TYPE` = 'BASE TABLE'
ORDER BY table_name DESC;
    );
PREPARE
    convert_engine;
FROM
    @convert_tables;
EXECUTE
    convert_engine;
DEALLOCATE
    convert_engine;
```
Created by Cory Gillenkirk