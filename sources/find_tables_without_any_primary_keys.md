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