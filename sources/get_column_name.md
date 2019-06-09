Looking to merge and automated adding virtual primary keys.
```
SET
    @column_name =(
    SELECT COLUMN_NAME
FROM
    information_schema.columns
WHERE
    table_schema = 'database_name' AND TABLE_NAME = 'table_name' AND ordinal_position = 56
);
SET
    @query = CONCAT(
        'ALTER TABLE `table_name` ADD `new_column_name` int(5) AFTER ',
        @column_name
    );
PREPARE
    stmt
FROM
    @query;
EXECUTE
    stmt;
DEALLOCATE
    stmt;
```

Source: https://stackoverflow.com/a/53316149/1926449