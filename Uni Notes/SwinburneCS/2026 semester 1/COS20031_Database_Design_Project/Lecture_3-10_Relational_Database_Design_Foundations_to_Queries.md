## Part 1: Why Relational Databases and the Design Process

### What is a Relational Database?
A **relational database** is a structured collection of data organised into tables where every fact is stored exactly once, and tables are linked to each other through keys rather than duplicating shared information. Each table represents one entity, each row represents one instance of that entity, and each column represents one atomic attribute of it.

In simple terms: instead of writing "John Lee, 22 Boundary Lane, Camberwell" on every order John places, you store his details once in a Customer table and reference them with a single identifier on each order row.
![[Pasted image 20260505135840.png]]
Think of it like: a set of linked reference books rather than one enormous notebook where everything is written out repeatedly. If John moves house, you update one row in one table and every order that references him automatically reflects the change.

Why it matters: flat storage (spreadsheets and single tables) inevitably produces three critical anomalies as data grows.
- An **insertion anomaly** means you cannot add a new fact without also supplying unrelated data; for example, you cannot record a new product's price without creating a fake order for it.
- An **update anomaly** means one real-world change requires updating many rows; if a customer's address is stored on every order row, changing it means updating potentially hundreds of rows and hoping none are missed.
- A **deletion anomaly** means removing one row accidentally destroys other information; if the only record of a product's unit price exists on a single order row, deleting that order destroys the price history. A well-designed relational schema prevents all three.

### Core Principles
![[Pasted image 20260505140521.png]]
**Atomicity** means every cell in a table holds exactly one indivisible value. 
- "John Lee" violates atomicity if the system ever needs to search by surname alone.
- "5 units" violates atomicity because it combines a quantity (integer) and a unit (text) into one cell.
- "$119 AUD" violates atomicity because it combines a number and a currency code.

Each of these must be split into separate columns. The practical rule is: if you ever need to search, sort, filter, or calculate on part of a value, that part must have its own column. Atomicity enables software to manipulate each piece of data independently, without human interpretation of mixed-format strings.

**No redundancy** means each fact is stored once. The moment a fact appears in two places, those two copies can diverge, and the database no longer has a single source of truth.

### The Design Process
![[Pasted image 20260505140723.png]]
Database design moves through three distinct phases
- **Conceptual modelling** produces a technology-neutral entity-relationship diagram that captures what entities exist, what attributes they carry, and how they relate. No DBMS (Database Management System) specific decisions are made at this stage.
- **Logical and physical modelling** translates the conceptual model into a concrete schema: data types are assigned, primary and foreign keys are defined, constraints are declared, and normalisation is applied.
- **Implementation** creates the schema using DDL (Data definition language) statements, populates it with data, and tests queries against real records. 

The input to all three phases is **requirements gathering**: interviews with stakeholders, review of existing systems, and documentation of business rules. Briefs from clients are often vague by design; the designer's job is to ask the right questions and translate natural language into precise schema decisions.

### Key Takeaways
1. Each of the three data anomalies is eliminated by storing each fact exactly once and referencing it everywhere else.
2. Atomicity is relative to the queries the database must answer; design for the questions you know you will need to ask.
3. Requirements gathering comes before any design work; every noun a stakeholder mentions is a candidate entity or attribute, and every verb is a candidate relationship or constraint.
4. The design process moves from conceptual (what exists) to logical/physical (how it is structured) to implementation (how it is created).

**Remember this:** Every problem a spreadsheet develops over time exists because data is duplicated rather than referenced.

**One analogy to remember it by:** A relational database is a reference library where one authoritative copy of each fact is updated in one place and consulted everywhere else. A flat table is a stack of photocopies that can all become wrong independently.

---

## Part 2: Entities, Keys, Cardinality and ER Diagrams

### Entities and Attributes
- An **entity** is any real-world concept that the database needs to store information about; it becomes a table.
- An **attribute** is a piece of information that describes an entity; it becomes a column.

When translating a plain English description such as "John Lee ordered 5 tablets on 13 January at $119 each, delivered 5 February" into a schema, the first step is identifying every distinct concept: the buyer, the product, the quantity, the price, and the delivery date are all attributes of an Order entity.
![[Pasted image 20260505142006.png]]
The buyer's name, once split into `Given_name` and `Family_name`, is a separate entity (Customer) because customers exist independently of any particular order.

### Primary Keys
Every table needs a **primary key**: an attribute or minimal combination of attributes whose value is unique across every row and never NULL. The primary key is the row's unambiguous identifier and is what foreign keys in other tables reference.

#### Natural key
A **natural key** uses real-world data as the identifier. A driver's licence number, a national ID, or a student number issued by an institution can all serve as natural keys when they are genuinely unique. The advantage is that the key is self-documenting. The risk is that natural values can change (a person changes their name), can collide in edge cases (two people named John Lee appear in the same table), and may require multiple columns to achieve uniqueness, producing a composite key.

#### Surrogate key
A **surrogate (thay thế) key** is an artificial integer created solely for uniqueness, with no real-world meaning. The DBMS generates it automatically using `AUTO_INCREMENT`. Surrogate keys are stable (they never change even if the real-world data changes), guaranteed unique, and simple (a single integer column). Their disadvantage is that they convey no information on their own.

In practice, most production tables use surrogate integer primary keys and enforce any meaningful natural identifiers (email addresses, licence numbers) as `UNIQUE` columns.

**Example:**
```sql
CREATE TABLE User (
    user_id   INT AUTO_INCREMENT PRIMARY KEY,
    email     VARCHAR(255) NOT NULL UNIQUE,
    username  VARCHAR(50)  NOT NULL UNIQUE,
    password  VARCHAR(255) NOT NULL
);
```
- **`user_id`** is the surrogate key. Every foreign key in other tables (orders, sessions, activity logs) will reference this number.
- **`email`** and **`username`** are natural identifiers that actually mean something. They are marked `UNIQUE` so no two users can share them, but they are not the primary key.

The reason this matters in practice: imagine a user changes their email from `john@old.com` to `john@new.com`. If email were the primary key, you would have to update it in the `User` table and in every other table that references it (orders, messages, logs, etc.), which could be thousands of rows, just too much. Because the primary key is `user_id`, changing the email only requires updating one column in one row. Everything else still correctly points to `user_id = 7` and nothing breaks. Note that other do not store email, just the id.

#### Composite key
A **composite key** spans multiple columns when no single column achieves uniqueness. The customer example with `(Given_name, Family_name, Phone_no)` as the composite key illustrates this: no single field is unique, but the combination is presumed unique per customer. Composite keys increase complexity when referenced as foreign keys, since every component must be carried across.

### Cardinality
**Cardinality** describes how many instances of one entity can relate to how many instances of another. It is expressed as a minimum and maximum on each side of the relationship line. To determine cardinality, ask two questions in both directions. 

For Customer and Order: "For each order, how many customers can it belong to?"Answer: exactly one.
![[Pasted image 20260505135840.png]]
Every order was placed by exactly one customer, so the Customer side is `1..1` (mandatory, singular). "For each customer, how many orders can they have?" Answer: zero or more. A brand-new customer has zero orders, while a loyal customer might have hundreds, so the Order side is `0..*` (optional, many). The full cardinality statement is `Customer 1..1 --- 0..* Order`.

The minimum value (0 or 1) answers whether the relationship is optional. A minimum of 0 means a row of that type can exist without the relationship being populated. A minimum of 1 means the relationship is mandatory.

### Foreign Keys and Removing Redundancy
A **foreign key** is a column whose values must match an existing primary key value in another table. It is the mechanism that physically enforces a cardinality relationship. Once `Order.cust_id` is declared as a foreign key referencing `Customer.cust_id` as shown up there, the DBMS prevents any order row from referencing a customer that does not exist.

![[Pasted image 20260505160355.png]]
The critical consequence: once a foreign key is in place, all data duplicated from the referenced table must be removed. If `Order` carries `cust_id` as a foreign key to `Customer`, there is no reason to also store `firstname` and `lastname` in `Order`. Those facts live in `Customer` and can always be retrieved through the key. Keeping them in both tables is redundancy, which produces update anomalies.

### ER Diagrams
An **ER diagram** is a visual blueprint of the schema. Each entity is a labelled rectangle listing its attributes. `[PK]` marks primary key attributes, `[FK]` marks foreign keys, and `[PK][FK]` marks attributes that are simultaneously part of the entity's own key and a reference to a parent.

![[Pasted image 20260505161338.png]]
Relationship lines show Chen's `min..max` cardinality notation at each end. Crow's feet notation conveys the same information graphically using bars (one), circles (zero), and crow's feet (many) at line ends.

![[Pasted image 20260505161449.png]]
When both ends of a relationship show `0..*`, a direct foreign key cannot represent the relationship; a **junction entity** (weak entity) is required between the two sides, with its own composite key incorporating the foreign keys of both parents.

### Key Takeaways
1. Natural keys are meaningful but fragile; surrogate keys are stable and simple; composite keys are necessary when no single attribute is unique.
2. Remove all columns from a table that are already present in a table it references via foreign key.
3. Cardinality is determined by asking "how many?" in both directions, not by guessing from the current data.
4. A `0..*` to `0..*` relationship always requires a junction table; it cannot be implemented with a direct foreign key.

**Remember this:** A foreign key is a constraint, not just a reference. It guarantees the referenced row exists.

**One analogy to remember it by:** A foreign key is a library borrowing record. It stores the card number, not the borrower's full details. The full details live in one place and are looked up through the number.

---

## Part 3: Weak Entities and Normalisation (1NF, 2NF, 3NF)

### Weak Entities
![[Pasted image 20260519004242.png]]
A many-to-many relationship can't be stored directly because foreign keys only enforce one-to-many. A student can enrol in many subjects, and a subject can have many students, so neither table can hold a foreign key to the other.

The fix is a junction table. `Enrolment` sits between `Student` and `Subject`, holding `studId [FK]` and `subjId [FK]` as a composite primary key. Each row represents one pairing.

But `studId + subjId` alone isn't enough here, because the same student can retake the same subject in a different semester or year. Adding `sem` and `year` to the composite key makes each enrolment instance unique.

`grade` doesn't belong to `Student` or `Subject` individually. It describes that specific enrolment, so it lives on `Enrolment`. Any attribute that belongs to the relationship rather than either parent always goes on the junction table.

### First Normal Form (1NF)
**1NF** requires every cell to contain exactly one atomic value and no repeating groups. Two distinct violations appear in the lecture.

![[Pasted image 20260519010913.png]]
The first is multiple values stacked in a single cell. A property inspection table where the row for property PG4 contains three inspection dates in one cell is not in 1NF. 
![[Pasted image 20260519011643.png]]
The fix is to flatten the table so each inspection becomes its own row.

![[Pasted image 20260519011659.png]]
The second is numbered columns: `comment_1`, `comment_2`, `comment_3`. This spreads the same concept across multiple columns. The problems are: you must decide in advance how many columns to create, later columns are mostly NULL, and querying for any comment containing a word requires checking every column separately. 
![[Pasted image 20260519011713.png]]
The fix is to add a `commentNo` column and give each comment its own row, extending the composite key to `(propNo, inspDate, commentNo)`. The rule of thumb is clear: if you find yourself numbering column names, you need rows instead of columns.

### Second Normal Form (2NF)
**2NF** requires that the table is in 1NF and every non-key attribute depends on the entire primary key, not just part of it. This issue only arises when the primary key is composite.
![[Pasted image 20260519011917.png]]
A **partial dependency** exists when a non-key attribute is determined by only a subset of the composite key. In a flat inspection table with composite key `(propNo, inspDate)`, the column `propAddress` depends only on `propNo`. The address is a property-level fact, not an inspection-level fact. It is duplicated on every inspection row for the same property, creating an update anomaly.
![[Pasted image 20260519011955.png]]
The fix is to extract `propNo` and `propAddress` into a separate `Property(propNo, propAddress)` table, leaving the inspection table with only attributes that genuinely depend on the full composite key.

### Third Normal Form (3NF)
**3NF** requires that the table is in 2NF and no non-key attribute depends on another non-key attribute.
![[Pasted image 20260519012011.png]]
A **transitive dependency** forms an indirect chain: `PK -> non-key A -> non-key B`. In the inspection table (now in 2NF), `staffNo` determines `sName`. Both are non-key attributes. `sName` does not depend directly on the primary key `(propNo, inspDate)`; it depends on `staffNo`, which in turn depends on the key. This transitive chain means `sName` is duplicated every time the same staff member appears in an inspection row. 
![[Pasted image 20260519012232.png]]
The fix is to extract `Staff(staffNo, sName)` into its own table and retain only `staffNo` as a foreign key in the inspection table.

### Comparing 2NF and 3NF

|Dependency Type|What depends on what?|Only possible when|
|---|---|---|
|Partial (violates 2NF)|Non-key depends on part of the PK|Composite key exists|
|Transitive (violates 3NF)|Non-key depends on another non-key|Any key type|

3NF is the standard target for production database design. Normal forms beyond 3NF (BCNF, 4NF, 5NF) address increasingly specialised dependency patterns and are noted for awareness rather than routine practice.

### Key Takeaways
1. 1NF: one value per cell, no numbered columns. Fix by adding rows and extending the key.
2. 2NF: no partial dependencies. Fix by separating out attributes that depend on only part of the composite key.
3. 3NF: no transitive dependencies. Fix by extracting non-key-to-non-key chains into their own table.
4. Normalisation always produces more tables, each with a tighter, more focused purpose.

**Remember this:** Partial dependencies violate 2NF; transitive dependencies violate 3NF. Apply them in order and the structure follows naturally.

**One analogy to remember it by:** Normalisation is organising a cluttered desk into labelled drawers where each drawer holds only what belongs there and nothing is duplicated across drawers.

---

## Part 4: DDL - Data Types, NULL, DEFAULT and Constraints

### What is DDL?
![[Pasted image 20260519012722.png]]
**Data Definition Language (DDL)** is the SQL subset used to define and modify database structure. `CREATE TABLE` is the primary statement. Each column requires a name, a data type, and a declaration of whether NULL is permitted. DDL choices made at this stage propagate throughout the entire project; a wrong data type or a missing constraint is expensive to fix once data is loaded.

### Data Types

| Category   | MySQL Types                             | Key Considerations                                                                   |
| ---------- | --------------------------------------- | ------------------------------------------------------------------------------------ |
| Integer    | `INT`, `TINYINT`, `SMALLINT`, `BIGINT`  | Use strings for postcodes and phone numbers; integers only when arithmetic is needed |
| Decimal    | `DECIMAL(p,s)`, `FLOAT`, `NUMERIC`      | Always define precision; prefer integer where no fraction is needed                  |
| Date/Time  | `DATE`, `TIME`, `DATETIME`, `TIMESTAMP` | Use `DATE` unless time of day is genuinely required                                  |
| String     | `CHAR(n)`, `VARCHAR(n)`, `TEXT`         | `CHAR` for fixed-length values; `VARCHAR` for variable-length text                   |
| Enum       | `ENUM('A','B',...)`                     | Best for columns with a small, fixed set of valid values                             |
| Binary/LOB | `BOOLEAN`, `BLOB`, `CLOB`               | For true/false flags and large binary or character objects                           |

`CHAR(n)` allocates exactly n bytes for every stored value, padding shorter values with spaces. `VARCHAR(n)` stores only as many bytes as the value actually contains plus a small overhead byte. Use `CHAR` for fixed-length codes such as postcodes and status flags; use `VARCHAR` for names, comments, and any free-text field.

### NULL
![[Pasted image 20260519012808.png]]
**NULL** is a special marker meaning "unknown" or "not applicable." It is not zero, not an empty string, and not a string of spaces. SQL operates under **ternary logic** when NULL is involved. A condition like `age > 65` returns UNKNOWN for a NULL age, silently excluding that row from results. The only correct way to test for NULL is `IS NULL` or `IS NOT NULL`. Using `= NULL` never matches anything.

### DEFAULT and ENUM
![[Pasted image 20260519012828.png]]
The `DEFAULT` keyword assigns a fallback value when no value is provided during an INSERT. Combined with `NOT NULL`, it eliminates accidental NULLs in columns that always have a sensible fallback:
```sql
gender CHAR(1) NOT NULL DEFAULT 'U'
-- even better:
gender ENUM('M', 'F', 'N', 'U') DEFAULT 'U'
```
`ENUM` restricts the column to a predefined set of valid values and enforces that restriction at the database level, rejecting any insert outside the defined set without requiring application-layer validation.

### Integrity Constraints
Constraints enforced in the database apply regardless of which application, script, or user modifies the data. They cannot be bypassed. Every business rule expressible as a constraint should be declared in DDL: `NOT NULL`, `UNIQUE`, `CHECK`, `DEFAULT`, `ENUM`, and `FOREIGN KEY ... REFERENCES` with appropriate `ON DELETE` behaviour (`RESTRICT` is the safe default; `CASCADE` and `SET NULL` are deliberate exceptions for specific use cases).

### Key Takeaways
1. NULL means unknown; ternary logic means NULL-bearing columns silently affect query results. Always use `IS NULL` to test.
2. `CHAR` is fixed-width and suited to codes; `VARCHAR` is variable-width and suited to text fields.
3. Use `NOT NULL DEFAULT` and `ENUM` together to prevent invalid and missing values at the database level.
4. Every enforceable business rule belongs in a DDL constraint, not in application code.

**Remember this:** A NULL in the wrong column is a data quality time bomb: it compiles, it runs, but it silently corrupts results.

**One analogy to remember it by:** NULL is an unlabelled box in a warehouse. A box labelled "0 items" and an empty box are different things, and both differ from a box with no label. Any system that treats the unlabelled box as empty will make the wrong decision.

---

## Part 5: DML - INSERT, UPDATE, DELETE and Transactions

### INSERT, UPDATE, DELETE
**Data Manipulation Language (DML)** is the SQL subset used to create, modify, and remove data rows. The three write operations are INSERT, UPDATE, and DELETE.

![[Pasted image 20260519013459.png]]
`INSERT INTO ... VALUES` adds a new row. Always list column names explicitly so the statement does not break if the table schema changes:
```sql
INSERT INTO Product (id, name, location, category)
VALUES ('LW36575', 'Lawn shredder', 'A5B17', 'Lawn mowers');
COMMIT;
```

![[Pasted image 20260519013522.png]]
`UPDATE ... SET ... WHERE` modifies existing rows. The `WHERE` clause restricts which rows are changed. Without it, every row in the table is updated:
```sql
UPDATE Order SET shipDate = '2016-01-26', custID = 'R234'
WHERE ordID = 1113;
COMMIT;
```

![[Pasted image 20260519013535.png]]
`DELETE FROM ... WHERE` removes rows. Without `WHERE`, every row in the table is deleted. If foreign key constraints are active, deleting a parent row that still has child rows will fail unless `ON DELETE CASCADE` or `ON DELETE SET NULL` is configured:
```sql
DELETE FROM Order WHERE ordID = 1113;
COMMIT;
```
The most dangerous SQL you will ever write has no `WHERE` clause.

### Autocommit and Transactions
![[Pasted image 20260519013648.png]]
By default in MySQL, **autocommit** is on: every DML statement is immediately and permanently committed the moment it executes. The lecture illustrates the consequence: `UPDATE Staff SET gender = 'F'` without a `WHERE` clause affected 45,364 rows in 0.05 seconds with no recovery possible.

![[Pasted image 20260519013807.png]]
A **transaction** is a sequence of DML statements treated as a single unit of work. Either all statements succeed and are committed, or none of them take effect. This is essential when two or more operations are logically inseparable: recording a stock arrival and updating the stock level must both succeed or neither should persist.

Two approaches exist in MySQL:
```sql
-- Option 1: disable autocommit for the session
SET AUTOCOMMIT = FALSE;
UPDATE Staff SET gender = 'F';        -- mistake noticed
ROLLBACK;                              -- fully undone
UPDATE Staff SET gender = 'F' WHERE staffID = 1234;
COMMIT;                                -- now permanent

-- Option 2: explicit transaction block
START TRANSACTION;
INSERT INTO supply (prod_id, quantity, arrival)
VALUES ('LW24453', 3000, '2025-09-26');
UPDATE stocklevel SET quantity = quantity + 3000
WHERE prod_id = 'LW24453';
COMMIT;
```

`COMMIT` makes all changes permanent and visible to other sessions. `ROLLBACK` undoes all changes back to the last commit. Until `COMMIT` is issued, other sessions see the pre-transaction state of the data.

### Key Takeaways
1. Always include `WHERE` in UPDATE and DELETE unless you intentionally want to affect every row.
2. Autocommit means there is no undo for individual statements; wrap risky or multi-step operations in an explicit transaction.
3. Any set of DML statements that must all succeed or all fail together belongs inside `START TRANSACTION ... COMMIT`.
4. `ROLLBACK` is only available when autocommit is off or a transaction is explicitly open.

**Remember this:** A transaction is a draft. Compose all changes, review them, and only when satisfied press Send (COMMIT). Until then, you can discard everything (ROLLBACK).

**One analogy to remember it by:** Autocommit is like sending a message the moment you type each word. Transactions are like composing the full message in a draft and pressing send only when you are certain it is correct.

---

## Part 6: SELECT - Querying the Database

### What is SELECT?
The `SELECT` statement retrieves data from one or more tables without modifying it. It is the most frequently used SQL command and the foundation of every database-driven application. Every SELECT produces a **result set**: a temporary table containing the matching rows and requested columns.

### Filtering with WHERE
![[Pasted image 20260519013918.png]]
The `WHERE` clause restricts which rows appear in the result set. A full range of predicate operators is available:
```sql
WHERE name LIKE 'A%'                   -- any name starting with A
WHERE name LIKE 'A_%'                  -- starts with A plus at least one more character
WHERE startDate BETWEEN '2025-10-01' AND '2025-10-31'
WHERE state IN ('WA', 'VIC', 'NSW')
WHERE state NOT IN ('WA', 'VIC', 'NSW')
WHERE salary >= 1000 AND age <= 50
WHERE salary >= 1000 OR position = 'manager'
WHERE state IS NULL                    -- correct way to test for NULL
WHERE state IS NOT NULL
```
`IS NULL` and `IS NOT NULL` are the only correct operators for testing NULL. This is a consequence of ternary logic: `state = NULL` never evaluates to TRUE because NULL is not equal to anything, including another NULL.

### Projection, Aliases and Concatenation
![[Pasted image 20260519013946.png]]
**Projection** means choosing which columns appear in the output rather than returning everything with `SELECT *`. Selecting only the needed columns reduces data transfer and makes result sets easier to interpret.

An **alias** renames a column or expression in the output using `AS`. **Concatenation** combines multiple column values or string literals into a single derived column (using `||` in standard SQL, or `CONCAT()` in MySQL):
```sql
SELECT category || ' - ' || name AS description, location AS shelf
FROM Product;
```
This produces a `description` column showing "Lawn mowers - Grass eliminator V5" by joining the category, a separator string, and the product name into one readable field.

### Aggregate Functions
![[Pasted image 20260519014010.png]]
**Aggregate functions** collapse a set of rows into a single summary value. The five standard functions are:

|Function|Returns|
|---|---|
|`COUNT(*)`|Total number of rows including those with NULLs in other columns|
|`COUNT(column)`|Number of rows where the specified column is not NULL|
|`SUM(column)`|Sum of all non-NULL values|
|`AVG(column)`|Arithmetic mean of non-NULL values|
|`MAX(column)`|Largest value|
|`MIN(column)`|Smallest value|

`COUNT(*)` and `COUNT(id)` are equivalent when every row has a non-NULL `id`. When a column can be NULL, the two counts can differ: `COUNT(*)` includes all rows; `COUNT(column)` excludes rows where that column is NULL.

### Grouping with GROUP BY
![[Pasted image 20260519014038.png]]
`GROUP BY` divides the result set into groups by the distinct values of one or more columns, and aggregate functions are then applied per group:
```sql
SELECT COUNT(*) AS count, category
FROM Product
GROUP BY category;
```

Given a Product table spread across "Lawn mowers", "Electric tools", and "Cleaners", this returns three rows: one per category with its count. Without `GROUP BY`, `COUNT(*)` collapses everything to a single number. A critical rule: any column that appears in the `SELECT` list but is not wrapped in an aggregate function must appear in the `GROUP BY` clause. MySQL may silently allow violations by picking an arbitrary value, producing misleading results. Most other DBMS reject the query outright. Writing `GROUP BY` correctly from the start avoids this entire class of subtle bug.

### Key Takeaways
1. Use `IS NULL` and `IS NOT NULL` to test for NULL; `= NULL` never matches due to ternary logic.
2. Projection, aliases, and concatenation make result sets more readable without changing the underlying data.
3. Aggregate functions collapse rows into summary values; `GROUP BY` controls how many result rows are produced.
4. Every non-aggregated column in a SELECT with aggregates must appear in the GROUP BY clause.
5. `COUNT(*)` counts all rows; `COUNT(column)` counts only non-NULL values in that column.

**Remember this:** Aggregates collapse rows; GROUP BY controls how many rows the result has. Without GROUP BY, all rows collapse to one.

**One analogy to remember it by:** `COUNT(*)` on a class roll tells you there are 30 students. `COUNT(*) GROUP BY subject` tells you 10 are in Maths, 12 in Science, and 8 in English. Same data, completely different question.

---

## Part 7: Indexes and Query Performance

### What is an Index?
An **index** is a separate data structure maintained alongside a table that maps the value of a chosen column to the physical address of the row containing that value. When a query filters on an indexed column, the DBMS can jump directly to the relevant rows instead of scanning every row in the table from start to finish.

In simple terms: instead of reading every page in a textbook looking for a topic, you check the index at the back, find the page number, and open directly to it.

Think of it like: a library card catalogue. The catalogue doesn't contain the books; it contains a sorted list of titles pointing to shelf locations. The DBMS uses the index the same way, looking up the address and fetching the row without inspecting everything else.

Why it matters: as tables grow to millions of rows, a full table scan becomes prohibitively slow. An index on the right column can reduce a query that scans every row down to a handful of lookups, making the difference between a query that runs in milliseconds and one that takes minutes.

![[Pasted image 20260519220941.png]]
Primary keys are automatically indexed by the DBMS. Every other column that needs fast lookup must be indexed explicitly by the designer.

### The B+Tree Structure
![[Pasted image 20260519222735.png]]
The default index structure in MySQL and MariaDB is a **B+ Tree**. When you define a primary key, the database automatically creates a B-tree index for it to enforce uniqueness and speed up data retrieval. 

The tree has a root node at the top, internal nodes that act as routing guides, and leaf nodes at the bottom that hold the actual key values paired with pointers to their row addresses.

To find a value, the DBMS starts at the root and follows the branch whose range contains the target value, descending level by level until it reaches a leaf. For a table with millions of rows, this traversal takes only a handful of comparisons rather than millions, because each level of the tree eliminates a large portion of the search space.

The B+Tree also supports range queries efficiently. Because leaf nodes are linked sequentially, a query like `BETWEEN '2025-10-01' AND '2025-10-31'` can find the start of the range in the tree and then scan forward through the leaf level without returning to the root.

### Selectivity
![[Pasted image 20260519222648.png]]
**Selectivity** describes how narrowly an index filters rows. A highly selective condition matches only a small fraction of the table; a low-selectivity condition matches most of it.

Indexing a column like `price` where only two distinct values exist (1000 and 2000) has low selectivity: a query for `price = 1000` still returns two-thirds of the table, and the DBMS may decide a full scan is faster than repeatedly following index pointers. Indexing a column like `orderID` where every row is unique has high selectivity: a lookup returns exactly one row and the index saves almost all the work.

The DBMS maintains internal statistics about column **cardinality (the numerical size of a set)** and uses them to decide whether to use an available index or ignore it in favour of a scan. Running `ANALYZE TABLE` forces the DBMS to recalculate those statistics when the data has changed significantly, keeping the query planner's decisions accurate.

### Choosing Which Column to Index
The process for selecting an index column is:

Examine the query's `WHERE` clause and identify every column that appears in a predicate. These are the columns the DBMS must evaluate to filter rows. In the example below, `QuotedPrice` and `OrderDate` are **predicate (filtering)** columns:
```sql
SELECT *
FROM Orders NATURAL JOIN Order_Details
WHERE QuotedPrice > 1000
AND OrderDate BETWEEN '2025-10-01' AND '2025-10-31';
```

Also identify any **foreign key columns** like `Order_Details.orderID` referencing `Orders.orderID` which used in the join condition. Join operations require the DBMS to match rows across tables. Without an index on it, every time the DBMS processes one row from `Orders`, it has to scan through the entire `Order_Details` table looking for matching `orderID` values. If `Orders` has 1000 rows, that's 1000 full scans of `Order_Details`:
```sql
Orders JOIN Order_Details ON Orders.orderID = Order_Details.orderID
```
An index on the join column avoids a full scan of the child table `Order_Details` for every row in the parent `Orders`.

Between the two predicate columns above, `OrderDate` is the better index candidate because a date range spanning one month is more selective than a price threshold that may match a large proportion of rows.

When you index the raw `OrderDate` column, the index stores every distinct date value, things like `2025-10-01`, `2025-10-02`, `2025-10-03`, and so on. If your table has orders spanning 5 years, that's potentially thousands of distinct (== low-selectivity) values in the index.

But if your queries always filter by month, like "give me all October orders" or "give me all March orders", you don't actually care about the exact date. You only care about which month it falls in. There are only 12 possible month values.

So instead of indexing the full date, you index the result of the `MONTH()` function that extract just the month from a date value:
```sql
ALTER TABLE `Order` ADD INDEX OrderDateIdx (MONTH(OrderDate));
```
Note that `OrderDateIdx` is just the **name** of the index, not a column.

Now the index only has 12 entries to work with instead of thousands of dates. It's smaller, takes less memory, and lookups are faster.

The `WHERE` clause filters on actual column values or expressions, so you have to write the same expression the index was built on, which is `MONTH(OrderDate)`. The DBMS then recognises that expression, sees there's an index built on it, and uses it automatically behind the scenes:
```sql
-- This uses the index
WHERE MONTH(OrderDate) = 10

-- This does NOT use the index, because the expression doesn't match
WHERE OrderDate BETWEEN '2025-10-01' AND '2025-10-31'
```
So the query and the index have to match on the same expression. If your queries use `BETWEEN` with full dates, index the raw column. If your queries always filter by month, index `MONTH(OrderDate)`. 

*The index can only be used when the expression in the query matches exactly what was indexed.*

### Why an Index on a Sort Column Helps
When a query uses `ORDER BY` on a column, MySQL normally loads all matching rows into memory and sorts them, this is called a **filesort**.

If an index exists on that column, MySQL can skip the filesort entirely. The index already stores values in sorted order across the B+ tree leaf nodes, so MySQL just reads the leaves left to right and gets rows in the correct order for free.

The rule is: if your query sorts by a column and no index exists on it, MySQL does a filesort. If an index exists, MySQL reads the index in order instead.
```sql
-- Index on the sort column
ALTER TABLE employees ADD INDEX idx_hire_date (hire_date);

-- EXPLAIN should show no "Using filesort" in the Extra column
EXPLAIN SELECT emp_no, first_name, last_name FROM employees ORDER BY hire_date;
```

_The index can only replace a filesort when the ORDER BY column matches exactly what was indexed._
### Creating, Testing and Dropping Indexes
Once the target column is identified, the index is created with `ALTER TABLE`:
```sql
ALTER TABLE `Order` ADD INDEX OrderDateIdx (OrderDate);
```

After creation, the `EXPLAIN` keyword prepended to a `SELECT` statement returns the query execution plan without running the query. The output shows whether the DBMS chose to use the new index, how many rows it estimated needing to examine, and which join strategy it selected. If the `key` column in the `EXPLAIN` output shows the index name, the index is being used.

```sql
EXPLAIN
SELECT *
FROM Orders NATURAL JOIN Order_Details
WHERE QuotedPrice > 1000
AND OrderDate BETWEEN '2025-10-01' AND '2025-10-31';
```

If `EXPLAIN` shows the index is not being used, it should be dropped. Indexes that are not used by any query still consume storage and, critically, slow down every `INSERT` and `UPDATE` on the table because the DBMS must update the index structure whenever a row changes. An unused index is a pure cost with no benefit:
```sql
ALTER TABLE `Order` DROP INDEX OrderDateIdx;
```

*The rule is: create an index, verify with `EXPLAIN` that it is used, and drop it if it is not.*

### Key Takeaways
1. An index maps column values to row addresses, allowing the DBMS to find rows without scanning the entire table.
2. The B+Tree structure supports both exact lookups and range queries efficiently by traversing from root to leaf.
3. Index selectivity determines usefulness: a low-selectivity column (few distinct values) is a poor index candidate because the DBMS may still scan most of the table.
4. Choose index columns by examining the `WHERE` predicate columns and foreign key join columns in your slowest queries.
5. Always verify an index is being used with `EXPLAIN`; drop it if it is not, because unused indexes slow down writes.

**Remember this:** An index speeds up reads but slows down writes. Only add one when you can prove with `EXPLAIN` that it is actually used.

**One analogy to remember it by:** Adding an index to a table is like adding a tab divider to a binder. Finding a section becomes instant, but every time you insert a new page you also have to update the tab system. If nobody ever uses the tab, you have added work for no gain.

---

## Part 8: AI Databases, Security, and Application Development

### Data Analytics and the OLTP/OLAP Split
A production database built for day-to-day operations is called an **OLTP** (Online Transaction Processing) database. Its design priorities are fast inserts, fast updates, data integrity through normalisation, and low latency for individual row lookups. Every principle covered in earlier parts of these notes, normalisation, foreign keys, constraints, and indexes, serves OLTP.

**OLAP** (Online Analytical Processing) describes a fundamentally different workload: aggregating millions of rows to answer strategic questions such as "what was total revenue by region across the last three years?" or "which equipment class has the highest average score per round?" These queries are slow and expensive on a normalised OLTP database because they scan large portions of many tables, touching data that write operations are simultaneously modifying.

The traditional solution is **data warehousing**: a second, separate database that exists purely for analysis. Your main OLTP database handles day-to-day operations like recording orders and updating scores, while the warehouse sits on the side answering big analytical questions like "what was total revenue by region across the last three years?" Because they are separate, analytical queries don't slow down live operations and vice versa.
![[Pasted image 20260519234721.png]]
Data moves from the OLTP database into the warehouse through a pipeline called **ETL** (Extract, Transform, Load):
- **Extract** - pull the data out of the main database
- **Transform** - restructure it into a flat or star-schema format that is easier to analyse
- **Load** - put the transformed data into the warehouse

The downside is that ETL only runs periodically, maybe once a day or once an hour. So the warehouse is always somewhat stale, only reflecting what happened up to the last time ETL ran. If a score was recorded 10 minutes ago, it may not show up in analytics yet.

### In-Memory Databases and Real-Time Analytics
![[Pasted image 20260520002507.png]]
The cutting-edge alternative to data warehousing is an **in-memory database**: a system that holds the entire working dataset in RAM rather than on disk. Because RAM access is orders of magnitude faster than disk I/O, these systems can run complex analytical queries against live operational data in milliseconds, eliminating the latency inherent in ETL pipelines.

**Kinetica** is a mature in-memory database used in production environments including the United States Federal Aviation Administration for air domain analytics. It supports standard SQL, offers a free developer edition, and provides built-in natural language to SQL generation, meaning a user can type a plain-English question and have the system produce and execute the corresponding query. For project purposes, the archery dataset can be uploaded to Kinetica's developer edition to test analytical queries and visualisations against real data.

The tradeoff with in-memory systems is cost and volatility: RAM is expensive, and data in memory is lost if the system loses power without a persistence mechanism. Production in-memory databases address this through *write-ahead logs (sequential log on persistent storage before the changes are applied to the primary data store)* and periodic snapshots to disk, but the architectural complexity is higher than a conventional disk-based DBMS.

|Approach|Where data lives|Query latency|Data freshness|Complexity|
|---|---|---|---|---|
|OLTP only|Disk|Fast for single rows, slow for aggregates|Always current|Low|
|Data warehouse (ETL)|Disk, separate system|Fast for aggregates|Stale by ETL interval|High|
|In-memory (Kinetica)|RAM|Fast for both|Always current|Medium|

### Database Security
#### Encryption
Two distinct layers of encryption apply to databases. Password hashing and Database encryption.

**Password hashing** protects user credentials stored in the database itself. Rather than storing a plaintext password, the application passes the input through a one-way hash function before storing it. When a user logs in, the input is hashed again and the hashes are compared. Even if an attacker reads the database, they see only hash values, not recoverable passwords:
```sql
INSERT INTO user VALUES (111, 'jane', SHA2('password', 256));
```

What actually gets stored in the database is not the word "password" but a long hash string like:
```
5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8
```

There is no way to reverse that back to "password". When Jane logs in, the system hashes whatever she types and compares it to the stored hash. If they match, she's in. If an attacker steals the database they only get that hash string, which is useless on its own.

Note that SHA5 as shown in the lecture slides is not a standard function; SHA2 with a 256-bit output is the correct MySQL equivalent. MD5 and SHA1 are considered cryptographically broken and should not be used for passwords.

**Database encryption** protects the stored files themselves. A *schema (== database in SQL)* can be created with encryption enabled so that the underlying data files on disk are unreadable without the encryption key, protecting against direct file system access:
```sql
CREATE SCHEMA db1 DEFAULT ENCRYPTION = 'y';
CREATE TABLE db1.user (id INT, name VARCHAR(100), password VARCHAR(50));
```
Note: `DEFAULT ENCRYPTION = 'y'` tells MySQL to automatically encrypt all tables inside the schema, making the data files on disk unreadable without the encryption key.

Without encryption, if someone physically stole the server or got access to the file system, they could open the raw database files and read the data directly, bypassing SQL entirely. Encryption makes those files unreadable without the key.

#### Access Control
MySQL's access control system separates **roles** from **users**. A role is a named collection of privileges; a user is a named account that can be assigned one or more roles. This separation means permissions are managed at the role level rather than per-user, making it straightforward to update access for an entire class of users by modifying one role.

The `GRANT` statement assigns specific DML (Data Manipulation Language) privileges on specific tables to a role:
```sql
CREATE ROLE accountant;
GRANT SELECT ON employee TO accountant;
GRANT SELECT, INSERT, UPDATE, DELETE ON salary TO accountant;
```

A user is then created and assigned the role:
```sql
CREATE USER jane IDENTIFIED BY 'initialpassword123';
GRANT accountant TO jane;
```
Note: Identified by set the user password

This means Jane can read the `employee` table and fully manage the `salary` table, but has no access to any other table.

You can define multiple roles for different user types and assign them accordingly:
```sql
-- A read-only analyst who can only view data
CREATE ROLE analyst;
GRANT SELECT ON employee TO analyst;
GRANT SELECT ON salary TO analyst;
GRANT SELECT ON department TO analyst;

-- A manager who can view and update but not delete
CREATE ROLE manager;
GRANT SELECT, UPDATE ON employee TO manager;
GRANT SELECT ON salary TO manager;

-- An admin who has full control
CREATE ROLE admin;
GRANT SELECT, INSERT, UPDATE, DELETE ON employee TO admin;
GRANT SELECT, INSERT, UPDATE, DELETE ON salary TO admin;
GRANT SELECT, INSERT, UPDATE, DELETE ON department TO admin;

-- Assign roles to users
CREATE USER bob IDENTIFIED BY 'password123';
CREATE USER alice IDENTIFIED BY 'password456';
CREATE USER superuser IDENTIFIED BY 'password789';

GRANT analyst TO bob;
GRANT manager TO alice;
GRANT admin TO superuser;
```

Now if the analyst role needs access to a new table, you update the role once and every user assigned that role gets the change automatically. Designing access by role enforces the principle of least privilege: each user class can only see and modify what their function requires.

#### SQL Injection
**SQL injection** is an attack where a malicious user supplies input that is interpreted as SQL rather than data. A login form that constructs a query by concatenating user input directly into a SQL string is vulnerable.

**Example 1: Bypassing login with OR 1=1**
```sql
-- If the username field receives: ' OR 1=1 --
-- The resulting query becomes:
SELECT * FROM users WHERE username = '' OR 1=1 --'
```
The condition `1=1` is always true, so the query returns every row in the table and the attacker bypasses authentication entirely. The `--` comments out the remainder of the query, including any password check.

**Example 2: Dropping a table**
```sql
-- If the username field receives: '; DROP TABLE users; --
-- The resulting query becomes:
SELECT * FROM users WHERE username = ''; DROP TABLE users; --'
```
This executes two statements: the original SELECT and then a DROP TABLE, permanently deleting the entire users table.

**Example 3: Extracting data from another table**
```sql
-- If the username field receives: ' UNION SELECT id, password, null FROM users --
-- The resulting query becomes:
SELECT * FROM orders WHERE customerID = '' UNION SELECT id, password, null FROM users --'
```
This appends a second query that pulls password data from the users table and returns it alongside the normal query results.

The correct defence is **prepared statements**, also called parameterised queries. The SQL structure is sent to the DBMS separately from the data values. The DBMS compiles the query template first and then inserts the values as data, making it structurally impossible for input to be interpreted as SQL:
```sql
-- Pseudocode for a prepared statement
PREPARE stmt FROM 'SELECT * FROM users WHERE username = ? AND password = ?';
EXECUTE stmt USING @input_username, @input_password;
```
Even if the user types `' OR 1=1 --` into the username field, the DBMS treats it as a literal string value to search for, not as SQL to execute. It looks for a username that literally equals the text `' OR 1=1 --`, finds nothing, and returns no rows.

**Stored procedures** provide a related defence: application code calls a named procedure stored in the database rather than constructing raw SQL, limiting what SQL the application layer can execute to a predefined set of operations:
```sql
-- Define the procedure once
CREATE PROCEDURE GetArcher(IN input_id INT)
BEGIN
    SELECT firstName, lastName, gender, yearBirth
    FROM Archer
    WHERE archerID = input_id;
END;

-- Application calls the procedure, never writes raw SQL
CALL GetArcher(1);
```
The application can only call `GetArcher` with an integer. There is no way to inject SQL through an integer parameter.
### Application Development Against the Database
The group project requires implementing a working user interface connected to the live database. The interface can be web-based, desktop, or mobile; the technical choice is secondary to demonstrating that the application correctly reads from and writes to the schema.

Two tiers of implementation are described in the lecture:
![[Pasted image 20260520010036.png]]
The baseline requirement is a score entry interface: given an archer, a round, and a distance, the user can enter arrow scores for each end and save them.

The more challenging option is a results display:
![[Pasted image 20260520010108.png]]
Querying the database to present a ranked leaderboard for a round

![[Pasted image 20260520010102.png]]
Or a personal bests listing per archer per equipment class, matching the kind of output real archery clubs produce from their scoring systems.

The submission for this component is a document of the implementation with screenshots. The interface does not need to be polished; demonstrating that SQL queries execute correctly through an application layer and that data written through the UI appears correctly in the database is what is being assessed.

### Key Takeaways
1. OLTP and OLAP are opposing design priorities; OLTP optimises for fast writes and integrity, OLAP optimises for fast reads across large datasets.
2. Data warehouses decouple analytical load from operational load but introduce data staleness via ETL; in-memory databases eliminate that tradeoff at higher cost.
3. Password hashing and database encryption address different threats: hashing protects credentials if the data is read; encryption protects the files if the disk is accessed directly.
4. Role-based access control enforces least privilege; grant roles to users rather than individual permissions to keep access management maintainable.
5. SQL injection is prevented by prepared statements, which separate SQL structure from data values structurally rather than relying on input sanitisation.

**Remember this:** Security is layered. Encryption protects the data at rest, access control limits who can query it, and prepared statements prevent attackers from rewriting the queries themselves.

**One analogy to remember it by:** A bank vault illustrates all three layers: the encryption is the vault door (data is unreadable without the key), access control is the security clearance list (only certain roles can enter), and prepared statements are the teller window (you make requests through a controlled interface and cannot walk into the vault yourself).