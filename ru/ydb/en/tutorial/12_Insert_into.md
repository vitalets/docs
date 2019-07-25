# Вставка данных с помощью INSERT

[INSERT](../../yql/reference/syntax/insert_into.md) is used to insert a new record.
While trying to insert a row with the existing primary key, an error
"Transaction rolled back due to constraint violation: insert_pk" will occur.

INSERT is less efficient than UPSERT and REPLACE.
While executing INSERT, {{ ydb-short-name }} makes read operation first to ensure that the primary
key value doesn't exist and then write operation.
Contrary, while executing UPSERT or REPLACE, {{ ydb-short-name }} makes only blind write.
It is highly recommended to use UPSERT or REPLACE instead of INSERT.

Task: insert data into the table.


{% note info %}

In the example we assume that you have created demo tables
on step [1.&nbsp;Create demo tables](01_Create_demo_tables.md) and populated them with the demo data
on step [2.&nbsp;Fill tables with data](02_Fill_tables_with_data.md).

{% endnote %}

```sql
INSERT INTO episodes
(
    series_id,
    season_id,
    episode_id,
    title,
    air_date
)
VALUES
(
    2,
    5,
    21,
    "Test 21",
    CAST(Date("2018-08-27") AS Uint64)
),
(
   -- you can insert multiple rows separated by commas at once
    2,
    5,
    22,
    "Test 22",
    CAST(Date("2018-08-27") AS Uint64)
)
;

COMMIT;

-- Let's take a look at the result.
SELECT * FROM episodes WHERE series_id = 2 AND season_id = 5;

COMMIT;
```