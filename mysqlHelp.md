
# Taking and restoring backup for a table
  - Taking backup:
    - `mysqldump -u -p mydatabase table1 > table1.sql`

  - restoring from backup flie need not include table name:
    - `mysql -u -p mydatabase < table1.sql`

# mysql cmd pretty print
 `pager less -SFX`
 
