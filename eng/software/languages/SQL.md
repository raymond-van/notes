## Syntax
| Operator | Condition                                                                                             | Example                                                                 |
| -------- | ----------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| %        | Used anywhere in a string to match a sequence of zero or more characters (only with LIKE or NOT LIKE) | col_name LIKE "%AT%"  <br>(matches "AT", "ATTIC", "CAT" or even "BATS") |
| -        | Used anywhere in a string to match a single character (only with LIKE or NOT LIKE)                    | col_name LIKE "AN_"  <br>(matches "AND", but not "AN")                  |
| DISTINCT | Remove duplicate rows                                                                                 |                                                                         |
| LIMIT    | Reduces the number of rows to return                                                                  |                                                                         |
| OFFSET   | Will specific where to begin counter the number of rows from (Combined with Limit)                    |                                                                         |
## Joins
- Inner (or just Join)
	- matches row from the first table and second table which have the same key
	- creates a result row with columns from both tables
	- resulting table only contains rows that have a match
- Left
	- A join that includes rows from A regardless if a matching row is found in B
- Right
	- A join that includes rows from B regardless if a matching row is found in A
- Outer
	- simply means that rows from both tables are kept, regardless of whether a matching row exists in the other table
- Cross
### Pagination
- Order by -> Limit offset