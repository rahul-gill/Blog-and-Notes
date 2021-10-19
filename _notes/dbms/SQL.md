---
layout: default
name: _notes/dbms/SQL.md
categories: Databases
date: 2021-10-19
---
<script 
    type="text/javascript"
    src="https://unpkg.com/mermaid@8.13.2/dist/mermaid.min.js">
</script>

<link 
  rel="stylesheet" 
  href="https://cdn.jsdelivr.net/npm/katex@0.13.18/dist/katex.min.css" integrity="sha384-zTROYFVGOfTw7JV7KUu8udsvW2fx4lWOsCEDqhBreBwlHI4ioVRtmIvEThzJHGET" crossorigin="anonymous">

<script defer 
  src="https://cdn.jsdelivr.net/npm/katex@0.13.18/dist/katex.min.js" integrity="sha384-GxNFqL3r9uRJQhR+47eDxuPoNE7yLftQM8LcxzgS4HT73tp970WS/wV5p8UzCOmb" crossorigin="anonymous">
</script>

<script defer 
  src="https://cdn.jsdelivr.net/npm/katex@0.13.18/dist/contrib/auto-render.min.js" integrity="sha384-vZTG03m+2yp6N6BNi5iM4rW4oIwk5DfcNdFfxkk9ZWpDriOkXX8voJBFrAO7MpVl" crossorigin="anonymous">
</script>
<script>
    document.addEventListener("DOMContentLoaded", function() {
        renderMathInElement(document.body, {
          // customised options
          // • auto-render specific keys, e.g.:
          delimiters: [
              {left: '$$', right: '$$', display: true},
              {left: '$', right: '$', display: false},
              {left: '\[', right: '\]', dispaly: true}
          ],
          // • rendering keys, e.g.:
          throwOnError : false
        });
    });
</script>
${toc}

# SQL Language
- declarative language: say what you want, not how you get it
- four sub-languages
    - DDL: data definition language(define and modify schema)
		- CREATE, ALTER, DROP, TRUNCATE, RENAME
    - DML: data manipulation language(queries)
		- SELECT, INSERT, UPDATE, DELETE, MERGE
	- DTL: data control language
		- GRANT, REVOKE
	- TCL: transaction control language
		- COMMIT, ROLLBACK, SAVEPOINT
- in SQL model of relation, it contains multi-set of rows(duplicate rows allowed)
- attributes have unique name
- every column have atomic type(which can't be further broken down)
- RDBMS: choose and run algorithms for declarative queries

# DDL

- defining table/relation

```sql
CREATE TABLE Students(
    sid INTEGER,
    sname CHAR(20),
    age FLOAT,
    PRIMARY KEY (sid)
);
CREATE TABLE Conferences(
    cid INTEGER,
    cname CHAR(20),
    topic CHAR(50),
    PRIMARY KEY (cid)
);
CREATE TABLE Meets(
    sid INTEGER,
    cid INTEGER,
    day DATE,
    PRIMARY KEY (sid, cid, day)
    FOREIGN KEY (sid)
        REFERENCES Students
    FOREIGN KEY (cid)
        REFERENCES Conferences
);
```

- primary key
    - provide unique lookup key for the relation
    - cannot have duplicate values
    - can be made up of more than one column
- foreign keys are like pointers to other table
    - by definition a foreign key references primary key of the other table
- `DROP TABLE Meets` will delete the table, `TRUNCATE TABLE Meets` will delete the data of the table, but not the table itself 
- altering tables
```sql
ALTER TABLE table_name 
	ADD column_name datatype; 
ALTER TABLE table_name 
	DROP COLUMN column_name; 
ALTER TABLE table_name 
	MODIFY COLUMN column_name datatype;
```
# Inserting, Deleting and Updating data 
```sql
INSERT INTO table_name (column1, column2, column3, ...) 
	VALUES (value1, value2, value3, ...); 
	
INSERT INTO table_name 
	VALUES (value1, value2, value3, ...); 
	
UPDATE Customers 
	SET ContactBill = 0, Free= 'yes' 
	WHERE CustomerID = 1;

DELETE FROM table_name WHERE condition; 
DELETE FROM table_name;
```
# Basic Simple Table Query
```sql
SELECT [DISTINCT] <column_expression_list>
    FROM <single_table>
    [WHERE <predicate> OR <predicate> AND NOT <predicate>]
    [ORDER BY <columns_list> [{ASC, DESC}]]
    [LIMIT <num_of_rows>];
```
- `<column_expression_list>` because we can apply arithmetic oerations etc. on columns and then return them.
- ordering by default is `ASC`
- LIMIT without ORDER BY is no-deterministic; will return random n rows
- example
```sql
SELECT DISTINCT S.name, S.gpa, S.age*2 AS a2
    FROM students AS S 
    WHERE S.dept = 'CS'
    ORDER BY S.gpa DESC, S.name ASC, a2
    LIMIT 3;
```
<br>

### aliases
- 2 ways
```sql
FROM tablea A, tableb B
FROM tablea as A, tableb as B
```
### arithmetic
- you can even use it like calculator(outputs a single row in the example)
```sql
SELECT 
    log(1000) as  three,
    exp(ln(2)) as two,
    cos(0) as one,
    ln(2*3) = ln(2) + ln(3 ) as sanity;
```
### string comparison
- `LIKE` operator. This query return `str`s with `snda` in it
```sql
SELECT str FROM STR_TABLE WHERE str LIKE '%snda%'
```
- for `LIKE` operator,  `%` represent 0/1/more characters, `_` represent one character
- regex: get sname where sname is like 'B.\*'(strings are single quoted in sql)
```sql
SELECT S.sname
FROM Sailors as S
WHERE S.sname REGEXP 'B.*'
```
- other may use different operator like PostgreSQL use `~`
### BETWEEN
- the operands can be numbers, text or dates and this operator is inclusive
```sql
SELECT column_name(s) 
	FROM table_name 
	WHERE column_name BETWEEN value1 AND value2; 
```
### aggregates
- like AVG, SUM, COUNT, MIN, MAX
- returns 1 row
- example
```sql
SELECT AVG(S.gpa) FROM Students S WHERE S.dept = 'CS'
```
### group by
```sql
SELECT AVG(S.gpa), S.dept
    FROM Students S
    GROUP BY S.dept
    HAVING COUNT(*) > 2
```
- in the example
    - returns tuples of (avg gpa of the department, department )
    - number of returned rows is equal to unique groups
    - having predicate filter groups which count is greater than two is true; only can be used in aggregate queries 
### sql evaluation order
- upto now
```sql
SELECT [DISTINCT] <column_expression_list>
FROM <single_table>
[WHERE <predicate>]
[GROUP BY <column_list>
[HAVING <predicate>]]
[ORDER BY <column_list>]
[LIMIT <integer_value>];
```
- evaluation order
<div class="mermaid">%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#ffff33', 'background': '#ff0000','titleColor':'#ffffff','nodeBorder':'#000000'}}}%%
flowchart LR;
    FROM --> WHERE --> SELECT
    SELECT--> a(GROUP BY) --> HAVING --> DISTINCT
    DISTINCT --> b(ORDER BY) --> LIMIT


</div>- here
    - FROM: identify the table
    - WHERE: filter out rows
    - SELECT: keep needed column in result
    - GROUP BY: form groups and aggregate
    - HAVING: filter out groups
    - DISTINCT: eliminate duplicates
    - ORDER BY and LIMIT: affect rendering of output

# Join queries(multiple tables)
- example(its like quering on cross product of the two table)
```sql
SELECT Sailors.id, Sailors.sname, Reserves.boatid
FROM Sailors, Reserves
WHERE Sailors.id = Reserves.sid
```
- to identify column of different table we do `tablea.col, tableb.col` or use aliases otherwise if the column name is only in one table we can use it without table name
- we can join the same table with aliases: **self join**
```sql
SELECT x.name, y.name, x.age - y.age
FROM tablea as x, tablea as y
WHERE x.age > y.age
```
### combining predicates
- returns nothing because color can't be green and red at once
```sql
SELECT R.sid
FROM Boats B, Reserves R
WHERE R.bid = B.bid AND
    (B.color = 'red' AND B.color = 'green');
```
- return sailor's sid which have reserved red and green boat
```sql
SELECT R.sid
FROM Boats B, Reserves R
WHERE R.bid = B.bid AND B.color = 'red'

INTERSECT

SELECT R.sid
FROM Boats B, Reserves R
WHERE R.bid = B.bid AND B.color = 'green';
```
- ALL tells to take duplicates(`UNION ALL`) if first have 4 copies and second 2 copies result will have 6 copies(summing up cardinality), INTERSECT: minimum of cardinality, EXCEPT: difference of cardinality
- `queryA UNION queryB` , `queryA INTERSECT queryB`, `queryA EXCEPT queryB`(take from A which are not in B)
### nested queries
- we have `IN`, `NOT IN`, `EXISTS`,`op ANY`,`op ALL`
- get sailor names which reserved boat with id 102
```sql
SELECT S.name FROM Sailors S WHERE S.sid IN
    (SELECT R.sid FROM Reserves R WHERE R.bid = 102)
```
- if we used EXISTS instead of IN, will return all of sailors' tuple if boat 102 is reserved or return empty result, better way is
```sql
SELECT S.name FROM Sailors S WHERE S.sid EXISTS
    (SELECT R.sid FROM Reserves R WHERE R.bid = 102 AND R.sid = S.sid)
```
- this is called correlated sub-query: there is a range variable(here S) that comes from outer block which is called correlation variable
- find sailors whose ratings are greater than some sailor named Popeye(use ALL instead of ANY if rating greater than all Popeyes)
```sql
SELECT * FROM Sailors S
WHERE S.rating > ANY(
    SELECT S2.rating FROM Sailors S2 WHERE S2.sname = 'Popeye'  
)
```
- also, these can be used this way:
```sql
SELECT * FROM Customers 
	WHERE Country IN ('Germany', 'France', 'UK'); 
```

- **relational division** find all the sailors who've reserved all boats: we found a pair of boat and sailor(first & second block) which don't have a reservation(innermost block), but output sailor name
	- innermost queries checks boats, which are reserved by the current sailor we're looking at
	- middle query checks boats, which are not reserved by the sailor
	- so the complete query is asking: find sailors such that for each of them, there is no such boat, which is not reserved by the sailor 
```sql
SELECT S.sname FROM Sailors S
WHERE NOT EXISTS(
    SELECT B.bid FROM Boats B
    WHERE NOT EXISTS(
        SELECT R.bid FROM Reserves R WHERE R.bid = B.bid AND R.sid = S.sid
    )
);
```

- **argmax** find the largest thing in a set of things

```sql
SELECT * FROM Sailors S
WHERE S.rating = (
    SELECT MAX(S2.rating) FROM Sailors S2
);
```

- argmax: returning only one row(will return arbitrary one)

```sql
SELECT * FROM Sailors S
ORDER BY rating DESC 
LIMIT 1;
```

## Types of join

#### inner/natural joins: all three queries are equivalent

```sql
SELECT s.sid, s.sname, r.bid
FROM Sailors S, Reserves R
WHERE S.sid = R.sid AND S.age > 20;

SELECT s.sid, s.sname, r.bid
FROM Sailors S INNER JOIN Reserves R
ON S.sid = R.sid WHERE S.age > 20;

SELECT s.sid, s.sname, r.bid
FROM Sailors S NATURAL JOIN Reserves R
WHERE S.age > 20;
```

- inner join is just the usual queries and have a little different syntax
- natural join have implicit ON condition based on attributes with same name(not a good idea to use natural join)

### outer joins

- return all matched rows and also unmatched rows from left/right/both tables based on LEFT/RIGHT/FULL OUTER join
- NULLs are filled in non-matching fields

```sql
SELECT s.sid, s.sname, r.bid
FROM Sailors S LEFT OUTER JOIN Reserves R
ON S.sid = R.sid;
```

# Views

- named queries; similar to macros or functions

```sql
CREATE VIEW view_name
AS select_statement;
```

- to simplifying things
- for security(access control)
- evaluated on fly(not stored)
- usage

```sql
SELECT * FROM view_name;

SELECT some_expr FROM view_name, some_table
```

- we can also just define sub-query in the `FROM` clause(views on the fly)

```sql
SELECT bname, scount
FROM Boats B, (
    SELECT B.bid, COUNT(*) FROM Boats B, Reserves R
    WHERE R.bid = B.bid AND B.color = 'red'
    GROUP BY B.bid
) AS Reds(bid, scount)
WHERE Reds.bid = B.bid AND scount < 10
```

- common table expression(CTE): expression define a table which can reused in subsequent parts on query; below query is same as above one

```sql
WITH Reds(bid, scount) AS(
    SELECT B.bid, COUNT(*) 
    FROM Boats B, Reserves R
    WHERE R.bid = B.bid AND B.color = 'red'
    GROUP BY B.bid
)
SELECT bname, scount
FROM Boats B, Reds
WHERE Reds.bid = B.bid AND scount < 10
```

- multiple CTEs; notice parentheses were just for clarity

```sql
WITH Reds(bid, scount) AS(
    SELECT B.bid, COUNT(*) 
    FROM Boats B, Reserves R
    WHERE R.bid = B.bid AND B.color = 'red'
    GROUP BY B.bid
),
UnpopularReds AS
    SELECT bname, scount
    FRON Boats B, Reds
    WHERE Reds.bid = B.bid AND scount < 10
SELECT * FROM UnpopularReds;
```

- groupby argmax example

```sql
WITH maxratings(age, maxrating) AS(
    SELECT age, MAX(rating)
    FROM Sailors
    GROUP BY age	
)
SELECT S.*
FROM Sailors S, maxratings m
WHERE S.age = m.age AND S.rating = m.maxrating
```

# NULL

- `x op NULL` always return `NULL`(e.g. `x <= NULL`) so instead use `IS NULL`, `IS NOT NULL`

```sql
WHERE rating IS NOT NULL
```

- if where clause evaluates to null, we treat it as false
- just see when we put True and False in place of Null, if the result it same that result could be used, else result will be Null
| NOT | T   | F   | N   |
| --- | --- | --- | --- |
|     | F   | T   | `N` |

| AND | T   | F   | N   |
| --- | --- | --- | --- |
| T   | T   | F   | `N` |
| F   | F   | F   | `F` |
| N   | `N` | `F` | `N` |

| OR  | T   | F   | N   |
| --- | --- | --- | --- |
| T   | T   | T   | `T` |
| F   | T   | F   | `N` |
| N   | `T` | `N` | `N` |

- `SELECT count(*) FROM Sailors` will add rows with NULLs
- `SELECT count(rating) FROM Sailors` will not count rows with NULL ratings