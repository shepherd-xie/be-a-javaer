To state it simply, why does query A return a result but B doesn't?
```
A: select 'true' where 3 in (1, 2, 3, null)
B: select 'true' where 3 not in (1, 2, null)
```
This was on SQL Server 2005. I also found that calling set ansi_nulls off causes B to return a result.

---

Query A is the same as:
```sql
select 'true' where 3 = 1 or 3 = 2 or 3 = 3 or 3 = null
```
Since `3 = 3` is true, you get a result.

Query B is the same as:
```sql
select 'true' where 3 <> 1 and 3 <> 2 and 3 <> null
```
When `ansi_nulls` is on, `3 <> null` is UNKNOWN, so the predicate evaluates to UNKNOWN, and you don't get any rows.

When `ansi_nulls` is off, `3 <> null` is true, so the predicate evaluates to true, and you get a row.