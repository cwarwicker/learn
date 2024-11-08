# MySQL Optimization

## Indexes

### Key Points
- Compound indexes require the left-most column, otherwise it won't be used
- Don't add too many indexes, as it'll slow down writes (unless that's a trade-off needed)
- Running EXPLAIN on test data can produce different results from production data, due to different statistics so MySQL makes different decisions. Make sure to run it on real data sets.

### EXPLAIN

`EXPLAIN <query>`

Using this example:

```
EXPLAIN
SELECT *
FROM BOOKS b 
JOIN AUTHORS a ON a.id = b.author 
WHERE b.genre = 'Fiction'
ORDER BY a.name
```

```
+----+-------------+-------+------------+--------+---------------+---------+---------+---------------------------------+------+----------+----------------------------------------------+
| id | select_type | table | partitions | type   | possible_keys | key     | key_len | ref                             | rows | filtered | Extra                                        |
+----+-------------+-------+------------+--------+---------------+---------+---------+---------------------------------+------+----------+----------------------------------------------+
|  1 | SIMPLE      | b     | NULL       | ALL    | ttlauthgen    | NULL    | NULL    | NULL                            |    2 |    50.00 | Using where; Using temporary; Using filesort |
|  1 | SIMPLE      | a     | NULL       | eq_ref | PRIMARY       | PRIMARY | 4       | sandbox_db.b.author |    1 |   100.00 | NULL                                         |
+----+-------------+-------+------------+--------+---------------+---------+---------+---------------------------------+------+----------+----------------------------------------------+
```

What do some of these things mean?
- `type` - See: Explain type
- `possible_keys` - Which keys/indexes it finds which MIGHT be used
- `key` - The key/index it chose to use
- `ref` - The value being compared to the index
- `rows` - An ESTIMATION of the amount of rows that will be processed
- `filtered` - An ESTIMATION of the % of rows which match the query criteria
- `extra` - See: Explain Extra


### EXPLAIN type (from best to worst)
- 🟢 `system` - Table only has one row or is empty
- 🟢 `const` - Can only be 1 record (e.g. primary/unique key) (e.g. `where id = 1`)
- 🟢 `eq_ref` - Join with another table which can only have 1 row returned
- 🟢 `ref` - Used an index with an equality operator (e.g. =  !=)
- 🟡 `fulltext` - Using a fulltext index (will rarely use)
- 🟡 `range` - Limited index scan, because it knows where to start (e.g. `where id > x AND id < y`)
- 🟡 `index` - Entire index is scanned to find a match
- 🔴 `all` - Entire table is scanned to find a match

### EXPLAIN extra
- 🟢 `using index` - Query can be satisifed using only an index
- 🟡 `using where` - A WHERE clause is used to restrict rows. Doesn't really mean much performance-wise.
- 🔴 `using filesort` - Sorting was needed instead of using an index. Ok if result set is small.
- 🔴 `using temporary` - A temp table was required. Ok if result set is small. If table is large, can use a lot of memory and/or disk space.


### Examples

```sql

-- create
CREATE TABLE BOOKS (
  id INTEGER PRIMARY KEY AUTO_INCREMENT,
  title VARCHAR(255) NOT NULL,
  author VARCHAR(255) NOT NULL,
  genre VARCHAR(255) NOT NULL,
  releasedate DATE NOT NULL,
  INDEX ttlauthgen (title, author, genre)
);


-- insert
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('The Title', 'Jim', 'Sci-fi', '2024-01-3');
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('Another Book', 'Dave', 'Fiction', '2024-01-22');
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('Still Here', 'Bob', 'Fiction', '2024-01-27');
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('Still Here', 'Jim', 'Sci-fi', '2024-01-5');
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('Another Book', 'Dave', 'Fiction', '2024-01-26');
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('The Title', 'Dave', 'Fantasy', '2024-01-29');
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('The Title', 'Bob', 'Sci-fi', '2024-01-21');
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('Still Here', 'Dave', 'Fiction', '2024-01-2');
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('The Title', 'Bob', 'Fiction', '2024-01-14');
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('Still Here', 'Jim', 'Fantasy', '2024-01-12');
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('The Title', 'Dave', 'Sci-fi', '2024-01-23');
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('Still Here', 'Bob', 'Fantasy', '2024-01-13');
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('Still Here', 'Dave', 'Fiction', '2024-01-26');
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('Another Book', 'Jim', 'Fantasy', '2024-01-28');
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('The Title', 'Dave', 'Sci-fi', '2024-01-24');
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('The Title', 'Bob', 'Fiction', '2024-01-25');
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('Another Book', 'Jim', 'Fiction', '2024-01-14');
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('Still Here', 'Bob', 'Fantasy', '2024-01-17');
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('The Title', 'Dave', 'Fantasy', '2024-01-17');
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('Another Book', 'Dave', 'Fiction', '2024-01-15');
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('Still Here', 'Bob', 'Sci-fi', '2024-01-15');
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('Still Here', 'Dave', 'Fantasy', '2024-01-21');
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('Another Book', 'Dave', 'Sci-fi', '2024-01-15');
INSERT INTO BOOKS (title, author, genre, releasedate) VALUES ('The Title', 'Bob', 'Sci-fi', '2024-01-1');



-- Full table scan (type: "ALL") [BAD]
EXPLAIN SELECT * FROM BOOKS;

-- Type: "const" (Uses a specific key with only 1 row it can ever return) [GOOD]
EXPLAIN SELECT * FROM BOOKS WHERE id = 7;

-- Type: "ref" - Using an indexed column (index: ttlauthgen). [GOOD]
EXPLAIN SELECT * FROM BOOKS WHERE title = 'Another Book';

-- Type: ALL (full scan) because LIKE can't use index with wildcard at the front. [BAD]
EXPLAIN SELECT * FROM BOOKS WHERE title LIKE '%5';

-- Type: range - Has to look through a range of rows. Though knows where to start. [NOT GREAT/ NOT AWFUL]
EXPLAIN SELECT * FROM BOOKS WHERE title LIKE 'The%';

-- Type: ref - Using the ttlauthgen index. [GOOD]
EXPLAIN SELECT * FROM BOOKS WHERE title = 'Still Here' AND genre = 'Fantasy';

-- Type: ref - Using the ttlauthgen index. [GOOD]
EXPLAIN SELECT * FROM BOOKS WHERE title = 'Still here' AND genre = 'Fantasy' AND author = 'Bob';

-- Type: "all" - Can't use the index because we are missing the left-most column (title).
EXPLAIN SELECT * FROM BOOKS WHERE author = 'Bob' AND genre = 'Fantasy';

-- Type: "ref" - We CAN use the index, even though we are missing the author column, because we have the left most column. [GOOD]
EXPLAIN SELECT * FROM BOOKS WHERE title = 'Dave' AND genre = 'Fiction';
```

### EXPLAIN vs EXPLAIN ANALYZE

- EXPLAIN will show you how the query is going to be run. EXPLAIN ANALYZE will actually run it so you can see the timings.
- Different formats for EXPLAIN are:
  - `EXPLAIN SELECT * FROM BOOKS`
    - 
    ```
    +----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------+
    | id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra |
    +----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------+
    |  1 | SIMPLE      | BOOKS | NULL       | ALL  | NULL          | NULL | NULL    | NULL |   24 |   100.00 | NULL  |
    +----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------+
    ```
  - `EXPLAIN format=tree SELECT * FROM BOOKS`
    - `-> Table scan on BOOKS  (cost=2.65 rows=24)`
  - `EXPLAIN format=json SELECT * FROM BOOKS`
    - ```
      "query_block": {
        "select_id": 1,
        "cost_info": {
          "query_cost": "2.65"
        },
        "table": {
          "table_name": "BOOKS",
          "access_type": "ALL",
          "rows_examined_per_scan": 24,
          "rows_produced_per_join": 24,
          "filtered": "100.00",
          "cost_info": {
            "read_cost": "0.25",
            "eval_cost": "2.40",
            "prefix_cost": "2.65",
            "data_read_per_join": "72K"
          },
          "used_columns": [
            "id",
            "title",
            "author",
            "genre",
            "releasedate"
          ]
        }
      }```
  - `EXPLAIN ANALYZE SELECT * FROM BOOKS`
    - `-> Table scan on BOOKS  (cost=2.65 rows=24) (actual time=0.026..0.052 rows=24 loops=1)`


## Execution Order

Query execution order is not the same order as your query is written.

For example:
```
EXPLAIN
SELECT *
FROM BOOKS b 
JOIN AUTHORS a ON a.id = b.author 
```

In this query we select from `b` and then join `a`, but the query plan for this is:

```
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+--------------------------------------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra                                      |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+--------------------------------------------+
|  1 | SIMPLE      | a     | NULL       | ALL  | PRIMARY       | NULL | NULL    | NULL |    2 |   100.00 | NULL                                       |
|  1 | SIMPLE      | b     | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    2 |    50.00 | Using where; Using join buffer (hash join) |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+--------------------------------------------+
```

So it selects from `a` first and then `b`, which is opposite. As MySQL chooses what it thinks is the best plan, based on number of disk reads.


## Configuration
- Storage Engine
  - `innodb` should be the default. `myisam` is deprecated and can cause crashes.
- `innodb_buffer_pool_size` - Used to store table caches, index caches, and other in-memory data
  - General rule of thumb is around 75% of available RAM. Needs frequent adjustment though.
  - [Calculate correct size](https://scalegrid.io/blog/mysql-innodb-buffer-pool-size/)
- `innodb_data_file_path` - Path to innodb data file
  - Set it to extend automatically, e.g. `innodb_data_file_path = ibdata1:12M:autoextend:max:10G`
- `innodb_log_file_size` - Used to recover data in event of a crash. Larger file = less time to recover.
  - General rule of thumn is around 25% of the `innodb_buffer_pool_size`
- `innodb_log_buffer_size` - Used to write to the log file
  - Advisable to leave as default `8M`
- `innodb_flush_log_at_trx_commit` - Method of flushing log when transaction commits
  - `1` for ACID compliance.
  - `0` and `2` for faster writes but no ACID compliance [See](https://docs.netapp.com/us-en/ontap-apps-dbs/mysql/mysql-innodb_flush_log_at_trx_commit.html)
- `innodb_lock_wait_timeout`
  - Time to wait for row locks. Default is `50` seconds
- `innodb_flush_method`
  - Method of flushing data. Default in Linux `fsync`. Recommened to change to `O_DIRECT` or `O_DSYNC` for faster I/O. Conduct benchmarks to see which is better for you.
