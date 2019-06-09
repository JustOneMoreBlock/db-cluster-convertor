Trying to get first or last column from a table.
```
SET @DATABASE_NAME = 'your_db';

SELECT COLUMN_NAME FROM information_schema.columns WHERE table_schema = @DATABASE_NAME AND table_name='tbladminperms';
```
Source: https://www.quora.com/How-do-I-show-column-names-in-MySQL