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
**Cardinality** describes how many instances of one entity can relate to how many instances of another. It is expressed as a minimum and maximum on each side of the relationship line.

To determine cardinality, ask two questions in both directions. For Customer and Order: "For each order, how many customers can it belong to?" Answer: exactly one.
![[Pasted image 20260505135840.png]]
Every order was placed by exactly one customer, so the Customer side is `1..1` (mandatory, singular). "For each customer, how many orders can they have?" Answer: zero or more. A brand-new customer has zero orders, while a loyal customer might have hundreds, so the Order side is `0..*` (optional, many). The full cardinality statement is `Customer 1..1 --- 0..* Order`.

The minimum value (0 or 1) answers whether the relationship is optional. A minimum of 0 means a row of that type can exist without the relationship being populated. A minimum of 1 means the relationship is mandatory.

### Foreign Keys and Removing Redundancy

A **foreign key** is a column whose values must match an existing primary key value in another table. It is the mechanism that physically enforces a cardinality relationship. Once `Order.cust_id` is declared as a foreign key referencing `Customer.cust_id`, the DBMS prevents any order row from referencing a customer that does not exist.

The critical consequence: once a foreign key is in place, all data duplicated from the referenced table must be removed. If `Order` carries `cust_id` as a foreign key to `Customer`, there is no reason to also store `firstname` and `lastname` in `Order`. Those facts live in `Customer` and can always be retrieved through the key. Keeping them in both tables is redundancy, which produces update anomalies.

### ER Diagrams

An **ER diagram** is a visual blueprint of the schema. Each entity is a labelled rectangle listing its attributes. `[PK]` marks primary key attributes, `[FK]` marks foreign keys, and `[PK][FK]` marks attributes that are simultaneously part of the entity's own key and a reference to a parent. Relationship lines show Chen's `min..max` cardinality notation at each end. Crow's feet notation conveys the same information graphically using bars (one), circles (zero), and crow's feet (many) at line ends.

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

_Reference: Moser, I. (2026) COS20031 Lecture Slides Weeks 5, 7; Connolly & Begg (textbook)_

---

### Weak Entities

A **weak entity** resolves a many-to-many relationship. The `Enrolment` table between `Student` and `Subject` is the classic example. A student can enrol in many subjects; a subject can have many students. Neither side can hold a foreign key to the other without violating the one-to-many constraint that foreign keys enforce. The junction table `Enrolment` sits between them and holds `studId [PK][FK]`, `subjId [PK][FK]`, `sem [PK]`, `year [PK]`, and `grade`. The four-column composite key is necessary because the same student can take the same subject in different semesters across different years, and each instance is a distinct record. Attributes that belong to the relationship itself rather than to either parent, such as `grade` or `hours_worked`, always go on the weak entity.

### First Normal Form (1NF)

**1NF** requires every cell to contain exactly one atomic value and no repeating groups. Two distinct violations appear in the lecture.

The first is multiple values stacked in a single cell. A property inspection table where the row for property PG4 contains three inspection dates in one cell is not in 1NF. The fix is to flatten the table so each inspection becomes its own row.

The second is numbered columns: `comment_1`, `comment_2`, `comment_3`. This spreads the same concept across multiple columns. The problems are: you must decide in advance how many columns to create, later columns are mostly NULL, and querying for any comment containing a word requires checking every column separately. The fix is to add a `commentNo` column and give each comment its own row, extending the composite key to `(propNo, inspDate, commentNo)`. The rule of thumb is clear: if you find yourself numbering column names, you need rows instead of columns.

### Second Normal Form (2NF)

**2NF** requires that the table is in 1NF and every non-key attribute depends on the entire primary key, not just part of it. This issue only arises when the primary key is composite.

A **partial dependency** exists when a non-key attribute is determined by only a subset of the composite key. In a flat inspection table with composite key `(propNo, inspDate)`, the column `propAddress` depends only on `propNo`. The address is a property-level fact, not an inspection-level fact. It is duplicated on every inspection row for the same property, creating an update anomaly. The fix is to extract `propNo` and `propAddress` into a separate `Property(propNo, propAddress)` table, leaving the inspection table with only attributes that genuinely depend on the full composite key.

### Third Normal Form (3NF)

**3NF** requires that the table is in 2NF and no non-key attribute depends on another non-key attribute.

A **transitive dependency** forms an indirect chain: `PK -> non-key A -> non-key B`. In the inspection table (now in 2NF), `staffNo` determines `sName`. Both are non-key attributes. `sName` does not depend directly on the primary key `(propNo, inspDate)`; it depends on `staffNo`, which in turn depends on the key. This transitive chain means `sName` is duplicated every time the same staff member appears in an inspection row. The fix is to extract `Staff(staffNo, sName)` into its own table and retain only `staffNo` as a foreign key in the inspection table.

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

_Reference: Moser, I. (2026) COS20031 Lecture Slides Weeks 7; LinkedIn Learning - MySQL Essential Training 2, MySQL Advanced Topics_

---

### What is DDL?

**Data Definition Language (DDL)** is the SQL subset used to define and modify database structure. `CREATE TABLE` is the primary statement. Each column requires a name, a data type, and a declaration of whether NULL is permitted. DDL choices made at this stage propagate throughout the entire project; a wrong data type or a missing constraint is expensive to fix once data is loaded.

### Data Types

|Category|MySQL Types|Key Considerations|
|---|---|---|
|Integer|`INT`, `TINYINT`, `SMALLINT`, `BIGINT`|Use strings for postcodes and phone numbers; integers only when arithmetic is needed|
|Decimal|`DECIMAL(p,s)`, `FLOAT`, `NUMERIC`|Always define precision; prefer integer where no fraction is needed|
|Date/Time|`DATE`, `TIME`, `DATETIME`, `TIMESTAMP`|Use `DATE` unless time of day is genuinely required|
|String|`CHAR(n)`, `VARCHAR(n)`, `TEXT`|`CHAR` for fixed-length values; `VARCHAR` for variable-length text|
|Enum|`ENUM('A','B',...)`|Best for columns with a small, fixed set of valid values|
|Binary/LOB|`BOOLEAN`, `BLOB`, `CLOB`|For true/false flags and large binary or character objects|

`CHAR(n)` allocates exactly n bytes for every stored value, padding shorter values with spaces. `VARCHAR(n)` stores only as many bytes as the value actually contains plus a small overhead byte. Use `CHAR` for fixed-length codes such as postcodes and status flags; use `VARCHAR` for names, comments, and any free-text field.

### NULL

**NULL** is a special marker meaning "unknown" or "not applicable." It is not zero, not an empty string, and not a string of spaces. SQL operates under **ternary logic** when NULL is involved. A condition like `age > 65` returns UNKNOWN for a NULL age, silently excluding that row from results. The only correct way to test for NULL is `IS NULL` or `IS NOT NULL`. Using `= NULL` never matches anything.

### DEFAULT and ENUM

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

_Reference: Moser, I. (2026) COS20031 Lecture Slides Weeks 7-8; LinkedIn Learning - MySQL Advanced Topics_

---

### INSERT, UPDATE, DELETE

**Data Manipulation Language (DML)** is the SQL subset used to create, modify, and remove data rows. The three write operations are INSERT, UPDATE, and DELETE.

`INSERT INTO ... VALUES` adds a new row. Always list column names explicitly so the statement does not break if the table schema changes:

```sql
INSERT INTO Product (id, name, location, category)
VALUES ('LW36575', 'Lawn shredder', 'A5B17', 'Lawn mowers');
COMMIT;
```

`UPDATE ... SET ... WHERE` modifies existing rows. The `WHERE` clause restricts which rows are changed. Without it, every row in the table is updated:

```sql
UPDATE Order SET shipDate = '2016-01-26', custID = 'R234'
WHERE ordID = 1113;
COMMIT;
```

`DELETE FROM ... WHERE` removes rows. Without `WHERE`, every row in the table is deleted. If foreign key constraints are active, deleting a parent row that still has child rows will fail unless `ON DELETE CASCADE` or `ON DELETE SET NULL` is configured:

```sql
DELETE FROM Order WHERE ordID = 1113;
COMMIT;
```

The most dangerous SQL you will ever write has no `WHERE` clause.

### Autocommit and Transactions

By default in MySQL, **autocommit** is on: every DML statement is immediately and permanently committed the moment it executes. The lecture illustrates the consequence: `UPDATE Staff SET gender = 'F'` without a `WHERE` clause affected 45,364 rows in 0.05 seconds with no recovery possible.

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

_Reference: Moser, I. (2026) COS20031 Lecture Slides Week 8; LinkedIn Learning - MySQL Essential Training 2, SQL Essential Training 2019_

---

### What is SELECT?

The `SELECT` statement retrieves data from one or more tables without modifying it. It is the most frequently used SQL command and the foundation of every database-driven application. Every SELECT produces a **result set**: a temporary table containing the matching rows and requested columns.

### Filtering with WHERE

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

**Projection** means choosing which columns appear in the output rather than returning everything with `SELECT *`. Selecting only the needed columns reduces data transfer and makes result sets easier to interpret.

An **alias** renames a column or expression in the output using `AS`. **Concatenation** combines multiple column values or string literals into a single derived column (using `||` in standard SQL, or `CONCAT()` in MySQL):

```sql
SELECT category || ' - ' || name AS description, location AS shelf
FROM Product;
```

This produces a `description` column showing "Lawn mowers - Grass eliminator V5" by joining the category, a separator string, and the product name into one readable field.

### Aggregate Functions

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
