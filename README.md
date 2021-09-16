A simple, customisable table audit system for PostgreSQL implemented using triggers.

This is based off https://github.com/hasura/audit-trigger with the following changes

1. `changed_fields` only store the column names that were updated; no values
2. `row_data` only stores key-value pairs of columns mentioned in `included_cols` parameter i.e. works in reverse manner to the original `ignored_cols`

## Installation

Load `audit.sql` into the database where you want to set up auditing. You can do this via psql or any other tool that lets you execute sql on the database.

```bash
psql -h <db-host> -p <db-port> -U <db-user> -d <db> -f audit.sql --single-transaction
```

## Setting up triggers

Run the following sql to setup audit on a table

```sql
select audit.audit_table('author');
```

For a table in a different schema name as follows:

```sql
select audit.audit_table('shipping.delivery');
```

This sets up triggers on the given table which logs any change (insert/update/delete) into the table `audit.logged_actions`.

```sql
select * from audit.logged_actions
```

## Available options

The function `audit.audit_table` takes the following arguments:

| argument | description |
| --- | --- |
| `target_table`     | Table name, schema qualified if not on search_path |
| `audit_rows`       | Record which columns changed and data for columns in `included_cols`, or only audit at a statement level |
| `audit_query_text` | Record the text of the client query that triggered the audit event? |
| `included_cols`    | Columns of data that will be recorded; default none |

### Examples

Log changed columns for every row, log the sql statement, but only log the data of the columns `id` and `company_id`

```sql
select audit.audit_table('author', true, true, '{id,company_id}');
```
