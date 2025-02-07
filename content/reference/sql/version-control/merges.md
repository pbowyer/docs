---
title: Merges
---

# Merging branches

To merge a branch into your current branch, use the [`DOLT_MERGE()` 
procedure](dolt-sql-procedures.md#dolt_merge):

```sql
CALL DOLT_MERGE('feature-branch');
```

This procedure returns whether the merge was a fast-forward and whether there are conflicts generated by the merge.

```
+--------------+-----------+
| fast_forward | conflicts |
+--------------+-----------+
| 1            | 0         |
+--------------+-----------+
```

Note, if you are running `DOLT_MERGE()` with `AUTOCOMMIT` on (the default of most clients), the merge will fail in the presence of conflicts. You must merge and clear conflicts in the same SQL transaction with default Dolt settings. To do this, you must disable `AUTOCOMMIT` and call `COMMIT` manually to complete a transaction. [Later in this document](./merges.md#committing-with-merge-conflicts) we will tell you how to change this setting for advanced use cases.

If your merge is not a fast-forward you are required to create a Dolt commit to persist the merge. For example, you can run `CALL DOLT_COMMIT('Merged feature-branch')`.

# Conflicts

Merging branches can create
[conflicts](../../concepts/dolt/conflicts.md), which you must resolve
before you can commit your transaction. If a merge creates conflicts,
the `DOLT_MERGE()` function will return a non-zero result in the conflicts column.

Merges can generate conflicts on schema or data. 

## Schema

Merges with schema conflicts cannot be resolved using SQL. The merge will fail. For instance a merge that adds the same column name with two different types will fail.

```sql
CALL DOLT_MERGE('schema-change');
Error 1105: schema conflicts for table test:
	two columns with the name 'c2'
```

## Data

Merges with data conflicts can be resolved using SQL. Conflicts must be resolved in the same SQL transaction by default. You can find which tables are in conflict by querying the `dolt_conflicts`
system table:

```sql
SELECT * FROM dolt_conflicts;
+--------+---------------+
| table  | num_conflicts |
+--------+---------------+
| people |             3 |
+--------+---------------+
```

Each database table has an associated `dolt_conflicts` table, which
you can `SELECT`, `UPDATE` and `DELETE` rows from to examine and
resolve conflicts. For the hypothetical `people` table above, the
conflict table looks like this:

```sql
DESCRIBE dolt_conflicts_people;
+------------------+-------------+------+------+---------+-------+
| Field            | Type        | Null | Key  | Default | Extra |
+------------------+-------------+------+------+---------+-------+``
| base_occupation  | varchar(32) | YES  |      |         |       |
| base_last_name   | varchar(64) | YES  |      |         |       |
| base_id          | int         | YES  |      |         |       |
| base_first_name  | varchar(32) | YES  |      |         |       |
| base_age         | int         | YES  |      |         |       |
| our_occupation   | varchar(32) | YES  |      |         |       |
| our_last_name    | varchar(64) | YES  |      |         |       |
| our_id           | int         | YES  |      |         |       |
| our_first_name   | varchar(32) | YES  |      |         |       |
| our_age          | int         | YES  |      |         |       |
| their_occupation | varchar(32) | YES  |      |         |       |
| their_last_name  | varchar(64) | YES  |      |         |       |
| their_id         | int         | YES  |      |         |       |
| their_first_name | varchar(32) | YES  |      |         |       |
| their_age        | int         | YES  |      |         |       |
+------------------+-------------+------+------+---------+-------+

SELECT * FROM dolt_conflicts_people;
+-----------------+----------------+---------+-----------------+----------+----------------+---------------+--------+----------------+---------+------------------+-----------------+----------+------------------+-----------+
| base_occupation | base_last_name | base_id | base_first_name | base_age | our_occupation | our_last_name | our_id | our_first_name | our_age | their_occupation | their_last_name | their_id | their_first_name | their_age |
+-----------------+----------------+---------+-----------------+----------+----------------+---------------+--------+----------------+---------+------------------+-----------------+----------+------------------+-----------+
| Homemaker       | Simpson        |       1 | Marge           |       37 | Homemaker      | Simpson       |      1 | Marge          |      36 | NULL             | NULL            |     NULL | NULL             |      NULL |
| Bartender       | Szslak         |       2 | Moe             |     NULL | Bartender      | Szslak        |      2 | Moe            |      62 | Bartender        | Szslak          |        2 | Moe              |        60 |
| NULL            | NULL           |    NULL | NULL            |     NULL | Student        | Simpson       |      3 | Bart           |      10 | Student          | Simpson         |        3 | Lisa             |         8 |
+-----------------+----------------+---------+-----------------+----------+----------------+---------------+--------+----------------+---------+------------------+-----------------+----------+------------------+-----------+
```

For each column in the database table, the `conflicts` table has three
columns: one for `base`, one for `ours` and one for `theirs`. These
represent the three different values you might choose to resolve the
conflict (either the common commit ancestor's values, the current
table values, or the merged table values).

## Resolving conflicts

To commit your transaction, you must first resolve the merge conflicts
by deleting every row in every `dolt_conflicts` system table. This
signals to Dolt that you have resolved every merge conflict to your
satisfaction. There are several different strategies you could use,
which you must repeat for every table in conflict.

### Take ours

To use the values in the current working set (rather than the merged
values), simply delete from the `dolt_conflicts` table without
changing anything else.

```sql
DELETE FROM dolt_conflicts_people;
```

### Take theirs

To use the merged values, overwriting our own, `REPLACE` and `DELETE`
rows from the table using the conflicts table:

```sql
-- Replace existing rows with rows taken with their_* values as long 
-- as their_id is not null (rows deleted in theirs)
REPLACE INTO people (id,first_name,last_name,age) (
    SELECT their_id, their_first_name, their_last_name, their_age
    FROM dolt_conflicts_people
    WHERE their_id IS NOT NULL
);

-- Delete any rows that are deleted in theirs
DELETE FROM PEOPLE WHERE id IN (
    SELECT base_id
    FROM dolt_conflicts
    WHERE base_id IS NOT NULL AND their_id IS NULL
);

-- mark conflicts resolved
DELETE FROM dolt_conflicts_people;
```

### Custom logic

It's also possible that you want your users to resolve conflicts
themselves by picking which of the conflicting values to use for each
row in conflict. You can build this workflow, or any other custom
logic you want, with the SQL primitives above.

## Committing with merge conflicts

By default, Dolt will not allow you to commit transactions that have
merge conflicts, and will automatically rollback any transaction with
merge conflicts when you attempt to `COMMIT` it. However, there may be
times when you need to commit merge conflicts, for example to
collaborate on resolving them with other people. To allow committing
merge conflicts, change this system variable to `1` for every client
that needs to commit merge conflicts:

```sql
set @@dolt_allow_commit_conflicts = 1;
```

The server will not allow you to create new Dolt commits (with the
[`dolt_commit()` system function](./dolt-sql-functions.md#dolt_commit)
or with the [`@@dolt_transaction_commit` system
variable](./dolt-sysvars.md#dolt_transaction_commit)) if the working
set has conflicts. You must resolve conflicts before creating a Dolt
commit.
