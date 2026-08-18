
### Creating a local user

In `user_administration` Schema.
```sql
SELECT user_administration.set_session_svg('dataman','2CS2');
```

In `user_administration` Schema.
```sql
SELECT user_administration.new_local_user('dataman');
```

`WXhCTwd67Q.sG0f`
`Hc5xwOUUp[b69uFT`

---

### Creating a subscription for replication after backup

In `user_administration` Schema from the local user.
```SQL
SELECT user_administration.get_replication()
```

In the restored Schema on PostgreSQL 18.
```sql
create subscription dataman_test_name 
connection 'dbname=dataman  
host=fuji.ucll.be  
port=52526  
user=local_studentnr  
password=autopassword  
sslmode=require'  
publication album_insert;
```

Insert Into statement to make sure everything works.
```SQL
INSERT INTO schema.table_name(column_name, column_name, column_name)
VALUES('value', 'value', 'value');
```

---

Views.

If you make a view like:

```sql 
CREATE VIEW big_table_count AS  
SELECT COUNT(*) AS total  
FROM big_table;
```

Then every time you do:

```sql
SELECT * FROM big_table_count;
```

PostgreSQL usually runs the `COUNT(*)` again.


Materialized view.

Create it:

```sql
CREATE MATERIALIZED VIEW big_table_count_mv AS  
SELECT COUNT(*) AS total  
FROM big_table;
```

Use it:

```sql
SELECT * FROM big_table_count_mv;
```

Now the result is stored physically.