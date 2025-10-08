
### **I. ER Model (Conceptual Database Design)** 
#### 1. **Objective of Data Modeling** 
- **Goal**: identify, understand, and represent business rules governing data in a clear, concise manner for developers and users, ensuring accurate database design. 
- **Tool**: ER (Entity-Relationship) model for conceptual design, representing entities, attributes, relationships, and constraints (e.g., modeling "Students-Courses" to derive the `Enrolled` table). 
#### 2. **Entity & Entity Set**
- **Entity**: A distinguishable real-world object (e.g., a student, a course) with attributes (e.g., `sid`, `name` for students).
- **Entity Set**: A collection of entities sharing the same attributes
- **Primary Key**: A minimal attribute set uniquely identifying an entity 
- **Candidate Key**: A set of attributes with unique values. 
#### 3. **Attribute Types** 
- **Simple vs. Composite**: Composite attributes have sub-attributes (e.g., `address` = `street` + `city`). 
- **Single-valued vs. Multi-valued**: Multi-valued attributes allow multiple values (e.g., `phone_numbers`, represented by double ellipses in ER diagrams, split into separate rows/tables in relations). 
- **Derived Attribute**: Calculated from other attributes (e.g., `age` from `birth_date`, represented by dashed ellipses in ER diagrams). 
#### 4. **Relationship & Relationship Set** 
- **Relationship**: describes the connection between entities
- **Relationship Set**: captures relationships of the same “type”
- **Cardinality Constraints**: 
	- define the numerical relationships between entities in a relationship set
	- 1:1 (e.g., "Employee-Unique Badge"), 1:M (e.g., "Department-Employees"), M:N (e.g., "Students-Courses"). 
- **Participation Constraints**: 
	- **Total Participation**: Every entity must participate (e.g., "Each student must take at least one course", shown as double lines in ER diagrams). 
	- **Partial Participation**: Allows non-participation (e.g., "Courses may have no students", shown as single lines). 
#### 5. **Special Relationships** 
- **Weak Entity**: Depends on a strong entity for existence, with a primary key combining the strong entity’s key and a partial key (e.g., "Dependents" of employees, key = `(eid, dependent_name)`). 
- **ISA Hierarchy**: Subclasses inherit attributes from superclasses (e.g., "Graduate Students" as a subclass of "Students"). 
	- Constraints: Disjoint (no overlap) or Covering (every superclass entity belongs to a subclass). 
- **Ternary Relationship**: Involves three entity sets (e.g., "Students-Courses-Teachers" for "enrollment-teaching" relationships). 
- **Aggregation:** treat a relationship set as an entity set for purposes of participation in other relationships
### **II. Relational Model (Logical Database Design)** 
#### 1. **Relational Database Basics** 
- **Relation**: A 2D table with **Schema** and **Instance**, with unique tuples (e.g., `Students(sid, name, gpa)`). 
- **Relational Database:** A relational database is a set of relations (tables)
- **Schema:** defines the structure of a relation, including its name and attributes
- **Instance:** a specific occurrence of a relation, containing rows and columns of data
- **Integrity Constraints**: 
	- rules enforced on a database to ensure data **accuracy** and **consistency**
	- **Entity Integrity**
		- **Primary Key**: `PRIMARY KEY`, non-null and unique (e.g., `CREATE TABLE Students (sid INT PRIMARY KEY)`). 
	- **Referential Integrity**
		- **Foreign Key**: `FOREIGN KEY REFERENCES`, ensures **referential integrity** with actions like `ON DELETE CASCADE` (e.g., `Enrolled(sid FOREIGN KEY REFERENCES Students)`). 
	- **Domain Integrity**
		- **CHECK & UNIQUE**: `CHECK (gpa BETWEEN 0 AND 4)`, `UNIQUE (email)`. 
#### 2. **ER to Relation Conversion** 
- **Entity Set**: Directly mapped to a table with its primary key (e.g., `Departments(did, dname, budget, PRIMARY KEY (did))`). 
- **1:1/1:M Relationship**: Foreign key in the "many" side table (e.g., `Employees(eid, name, dept_id FOREIGN KEY REFERENCES Departments)` for 1:M "Department-Employees"). 
- **M:N Relationship**: Create an association table with a composite key (e.g., `Takes(sid, cid, score, PRIMARY KEY (sid, cid))`). 
- **Weak Entity**: Combined with the identifying relationship, key includes the strong entity’s key (e.g., `Dependent(eid, dname, age, PRIMARY KEY (eid, dname), FOREIGN KEY (eid))`). 
### **III. Relational Algebra** 
#### 1. **Basic Operators** 
- **Projection (`π`)**: Select attributes, remove duplicates (e.g., `π_name, age(Students)`). 
- **Selection (`σ`)**: Filter tuples (e.g., `σ_rating>8(Sailors)` for sailors with rating > 8). 
- **Cartesian Product (`×`)**: Combines all tuples (e.g., `Sailors × Reserves`, filtered by `sid` for meaningful results).
- **Set Operations**: Union (`∪`), Intersection (`∩`), Difference (`−`), requiring compatible attributes (e.g., `π_sid(Enrolled) − π_sid(Dropped)` for active students). 
#### 2. **Advanced Operators** 
- **Natural Join (`⋈`)**: Joins on common attributes (e.g., `Sailors ⋈ Reserves` = `SELECT * FROM Sailors, Reserves WHERE Sailors.sid = Reserves.sid`). 
- **Division (`÷`)**: Find tuples satisfying all conditions (e.g., `Takes ÷ Courses` for students who took all courses, via `π_sid,cid(Takes) ÷ π_cid(Courses)`). 
- **Renaming (`ρ`)**: Rename tables/attributes (e.g., `ρ_Temp(sid, bid)(Reserves)`). 
### **IV. SQL (Basic & Advanced)** 
#### 1. **Basic Queries** 
- **Projection/Filter**: `SELECT name, gpa FROM Students WHERE gpa > 3.0 ORDER BY gpa DESC`. 
- **Joins**:
	- **Inner Join**: Default join (e.g., `SELECT S.name, C.cid FROM Students S JOIN Enrolled E ON S.sid = E.sid JOIN Courses C ON E.cid = C.cid`).
	- **Outer Joins**: 
		- **LEFT JOIN**: Retain all left table rows (e.g., `Students LEFT JOIN Enrolled` to show students without enrollments). - 
		- **RIGHT/FULL JOIN**: Retain right table/all rows, respectively. 
- **Subqueries**:
	- `IN`/`NOT IN`: Membership check (e.g., `SELECT * FROM Students WHERE sid IN (SELECT sid FROM Enrolled WHERE cid = 'COMP101')`). 
	- `EXISTS`: Existence check (e.g., `SELECT * FROM Courses C WHERE EXISTS (SELECT * FROM Enrolled E WHERE E.cid = C.cid)` for courses with students). 
#### 2. **Aggregation & Grouping** 
- **Aggregate Functions**: `COUNT(*), SUM(score), AVG(gpa)`. 
- **GROUP BY & HAVING**: Filter groups (e.g., `SELECT dept_id, AVG(salary) FROM Employees GROUP BY dept_id HAVING AVG(salary) > 50000`). 
#### 3. **Updates & Triggers** 
- **CRUD Operations**: Respect foreign key constraints (e.g., `ON DELETE CASCADE` deletes dependent rows). 
- **Trigger**: a special type of stored procedure that automatically executes in response to certain events on a table (e.g., update course enrollment count on insert: 
```sql 
CREATE TRIGGER UpdateEnrollCount 
AFTER INSERT ON Enrolled 
FOR EACH ROW 
BEGIN
UPDATE Courses SET enroll_count = enroll_count + 1 
WHERE cid = NEW.cid; 
END;
``` 
### **V. Functional Dependency (FD) & Normalization** 
#### 1. **Problems with Redundancy** 
- **Issues**: Wasted storage, update overhead, update anomalies. 
#### 2. **Functional Dependency** 
- **Definition**: `X → Y` means Y is uniquely determined by X (e.g., `student_id → name`, `(sid, cid) → score`). 
- **Types**: 
	- **Trivial FD**: `Y ⊆ X` (e.g., `(id, name) → id`).
	- **Transitive FD**: `X → Y` and `Y → Z` (e.g., `emp_id → dept_id → dept_name` implies `emp_id → dept_name`). 
#### 3. **Normalization Steps** 
- **1NF**: Eliminate multi-valued attributes (e.g., split `phone_numbers` into a separate table). 
- **2NF**: Eliminate partial dependencies (e.g., split `Students(sid, cid, name)` into `Students(sid, name)` and `Enrolled(sid, cid, score)`). 
- **3NF**: Eliminate transitive dependencies (e.g., split `Employees(emp_id, dept_id, dept_name)` into `Employees(emp_id, dept_id)` and `Departments(dept_id, dept_name)`). 
### **VI. View & Outer Join** 
#### 1. **View** 
- **Virtual Table**: Based on queries, not stored physically (e.g., 
```sql 
CREATE VIEW HighGPAStudents AS 
SELECT sid, name, gpa FROM Students
WHERE gpa >= 3.5; 
```
- **Use Cases**: Simplify complex queries, restrict data access (e.g., hide sensitive fields like salary). 
#### 2. **Outer Join** 
- **LEFT JOIN**: Retain all left table rows, NULL for unmatched right rows (e.g., `Sailors LEFT JOIN Reserves`). 
- **RIGHT JOIN**: Retain all right table rows, NULL for unmatched left rows. 
- **FULL JOIN**: Retain all rows from both tables, NULL for unmatched rows (database-dependent, e.g., supported in SQL Server). 
```sql
SELECT (column_list) 
FROM table_name 
	[INNER | {LEFT |RIGHT | FULL } OUTER] JOIN table_name 
	ON qualification_list 
WHERE …
```
### **VII. XML Data Model** 
represents data in a hierarchical tree structure. Data is organized using tags, attributes, and elements, making it both human-readable and machine-processable
#### 1. **DTD (Document Type Definition)** 
- a schema for an XML data model
- **Element Declaration**: Define XML structure (e.g., 
```dtd 
<!ELEMENT movies (movie*)> 
<!ELEMENT movie (title, price, theater?)> 
<!ATTLIST movie mid CDATA #REQUIRED> 
```
- **Attribute Types**: `#REQUIRED` (mandatory), `#IMPLIED` (optional). 
#### 2. **XQuery**
- **Path Expression**: Select nodes (e.g., `doc("db.xml")/movies/movie/title` for all movie titles). 
- **Conditional Query**: Filter with `FOR...WHERE...RETURN` (e.g., 
```xquery
FOR $m IN doc("db.xml")/movies/movie
WHERE $m/price > 50
RETURN <Result>{$m/title}</Result> 
``` 
