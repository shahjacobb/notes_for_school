### Determining Primary Key

The primary key is a set of attributes (or a single one) that uniquely identifies each row in the relation.

The main thing about the primary key is whether it's a set of attributes or a single attribute, if you specify a value for the PK's attributes, and you return more than 1 row, that set of attributes is **not** the PK. 

The primary key should be irreducible (as few attributes as possible) as well (kind of obvious, but throwing it in there).

Here's an example for determining the PK.

| Student_ID | Course_ID | Semester | Year | Grade |
| ---------- | --------- | -------- | ---- | ----- |
| 1001       | CS101     | Fall     | 2022 | A     |
| 1001       | CS102     | Spring   | 2023 | B     |
| 1002       | CS101     | Fall     | 2022 | B     |
| 1002       | CS102     | Spring   | 2023 | A     |
| 1001       | CS101     | Fall     | 2023 | A     |

There's no functional or transitive dependencies here. Anyways, what would the PK be? `StudentID` isn't enough, neither is `CourseID`. Even the combination of `Student` and `CourseID` isn't enough because a student can retake a class. The PK is the set of the first four attributes (excluding grade).

### Functional Dependencies
A functional dependency is when you have a set of attributes $\bar A$ and set of attributes $\bar B$ where A uniquely determines B. In other words, if I have a specific attribute in $\bar A$ called $\bar a$, the value of $\bar b$ is completely dependent on it.

Here's a crappy drawing below. If there are two duplicate tuples, then the value of $\bar b$ *has* to be the same.

![[Pasted image 20240312230408.png]]
Functional dependencies are determined based of assumptions in the real world, or assumptions you make up. Another example is a college admissions table. If I say something like

``` 
1 if GPA > 3.7
2 if GPA > 3.2 and < 3.7
3 if GPA > 2.8 and < 3.2

```

And we assume that GPA is $A$ and Priority is $B$. Then we made up a dependency. It doesn't though, because functional dependencies only make sense in the real world. 

## Normalization Steps

Assume we have this table that needs to go through the normalization process. 
1. **1NF**
	* The rules of 1NF are: a relation cannot have repeating groups, has to have non-atomic cells, and **every row must be unique**
2. 2NF 
	- The rules for 2NF remove any **partial dependencies**. A partial dependency is a functional dependency where a non-key attribute is dependent on a subset (PORTION) of the composite key. 
		- This also means if your PK is one column, by definition you can't have partial dependencies.

Let's say I have this table:

**Book Loans (1NF)**

|Loan_ID|Book_Title|Author_Name|Borrower_Name|Borrower_Phone|Loan_Date|Return_Date|Branch_Name|Branch_Street|Branch_City|Branch_State|Branch_Zip|Branch_Phone|
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|1|Database Management Systems|Ramez Elmasri|John Doe|555-1234|2023-05-01|2023-06-01|Main Branch|123 Main St|Cityville|ST|12345|555-1111|
|1|Database Management Systems|Shamkant Navathe|John Doe|555-5678|2023-05-01|2023-06-01|Main Branch|123 Main St|Cityville|ST|12345|555-1111|
|2|Introduction to Algorithms|Thomas H. Cormen|Jane Smith|555-2468|2023-05-02|2023-06-02|Downtown Branch|456 Elm St|Cityville|ST|12345|555-2222|
|2|Introduction to Algorithms|Charles E. Leiserson|Jane Smith|555-2468|2023-05-02|2023-06-02|Downtown Branch|456 Elm St|Cityville|ST|12345|555-2222|
|2|Introduction to Algorithms|Ronald L. Rivest|Jane Smith|555-2468|2023-05-02|2023-06-02|Downtown Branch|456 Elm St|Cityville|ST|12345|555-2222|
|2|Introduction to Algorithms|Clifford Stein|Jane Smith|555-2468|2023-05-02|2023-06-02|Downtown Branch|456 Elm St|Cityville|ST|12345|555-2222|
|3|Database System Concepts|Abraham Silberschatz|Bob Johnson|555-3690|2023-05-03|2023-06-03|Main Branch|123 Main St|Cityville|ST|12345|555-1111|
|3|Database System Concepts|Henry F. Korth|Bob Johnson|555-3690|2023-05-03|2023-06-03|Main Branch|123 Main St|Cityville|ST|12345|555-1111|
|3|Database System Concepts|S. Sudarshan|Bob Johnson|555-3690|2023-05-03|2023-06-03|Main Branch|123 Main St|Cityville|ST|12345|555-1111|
|4|Fundamentals of Database Systems|Ramez Elmasri|Alice Brown|555-4321|2023-05-04|2023-06-04|Uptown Branch|789 Oak St|Cityville|ST|12345|555-3333|
|4|Fundamentals of Database Systems|Shamkant Navathe|Alice Brown|555-4321|2023-05-04|2023-06-04|Uptown Branch|789 Oak St|Cityville|ST|12345|555-3333|

The primary key is a composite key **LoanID, Author_Name**. The reason it's not just obviously loanID is because the textbooks have more than one author, and we were violating atomicity in 1NF. 

Now we need to scan for any partial dependencies. Basically every column but `Author_Name` is ultimately dependent on Loan_ID, so that's one partial. The other partial dependency is obviously `Book_Title` on `Author_Name`. So we split it up into two tables.

**Loans_2NF**

| Loan_ID | Book_Title                       | Borrower_Name | Borrower_Phone | Loan_Date  | Return_Date | Branch_Name     | Branch_Street | Branch_City | Branch_State | Branch_Zip | Branch_Phone |
| ------- | -------------------------------- | ------------- | -------------- | ---------- | ----------- | --------------- | ------------- | ----------- | ------------ | ---------- | ------------ |
| 1       | Database Management Systems      | John Doe      | 555-1234       | 2023-05-01 | 2023-06-01  | Main Branch     | 123 Main St   | Cityville   | ST           | 12345      | 555-1111     |
| 1       | Database Management Systems      | John Doe      | 555-5678       | 2023-05-01 | 2023-06-01  | Main Branch     | 123 Main St   | Cityville   | ST           | 12345      | 555-1111     |
| 2       | Introduction to Algorithms       | Jane Smith    | 555-2468       | 2023-05-02 | 2023-06-02  | Downtown Branch | 456 Elm St    | Cityville   | ST           | 12345      | 555-2222     |
| 3       | Database System Concepts         | Bob Johnson   | 555-3690       | 2023-05-03 | 2023-06-03  | Main Branch     | 123 Main St   | Cityville   | ST           | 12345      | 555-1111     |
| 4       | Fundamentals of Database Systems | Alice Brown   | 555-4321       | 2023-05-04 | 2023-06-04  | Uptown Branch   | 789 Oak St    | Cityville   | ST           | 12345      | 555-3333     |
|         |                                  |               |                |            |             |                 |               |             |              |            |              |

**Titles_2NF**

| Book_Title                       | Author_Name          |
| -------------------------------- | -------------------- |
| Database Management Systems      | Ramez Elmasri        |
| Database Management Systems      | Shamkant Navathe     |
| Introduction to Algorithms       | Thomas H. Cormen     |
| Introduction to Algorithms       | Charles E. Leiserson |
| Introduction to Algorithms       | Ronald L. Rivest     |
| Introduction to Algorithms       | Clifford Stein       |
| Database System Concepts         | Abraham Silberschatz |
| Database System Concepts         | Henry F. Korth       |
| Database System Concepts         | S. Sudarshan         |
| Fundamentals of Database Systems | Ramez Elmasri        |
| Fundamentals of Database Systems | Shamkant Navathe     |


3. **3NF** 
	- 3NF looks for any transitive dependencies. A transitive dependency is a $C$ non-key attribute that's dependent on a non-key attribute $B$, *which is* dependent on a primary key attribute $A$. We need to find it and create separate tables for it, column B as the foreign key in the OG table and the PK in the new one.  
	- So addressing the previous example, let's look at both tables. The **Titles_2NF** table has no transitive dependencies, so let's ignore that. **Loans_2NF** though, has a few. 
	- *Specifically:*
		- return_date is functionally dependent on loan_date, which is transitively dependent on loan_id
		- everything branch related is functionally dependent on branch_date, which is transitively dependent on loan_id
		- borrower_number is functionally dependent on borrower_name, which is transitively dependent on loan_id
	- So that's **3 tables.** And they look like:

**Book_Loans_3NF**

|Loan_ID|Book_Title|Borrower_Name|Loan_Date|Branch_Name|
|---|---|---|---|---|
|1|Database Management Systems|John Doe|2023-05-01|Main Branch|
|2|Introduction to Algorithms|Jane Smith|2023-05-02|Downtown Branch|
|3|Database System Concepts|Bob Johnson|2023-05-03|Main Branch|
|4|Fundamentals of Database Systems|Alice Brown|2023-05-04|Uptown Branch|

**Borrowers_3NF**

|Borrower_Name|Borrower_Phone|
|---|---|
|John Doe|555-1234|
|John Doe|555-5678|
|Jane Smith|555-2468|
|Bob Johnson|555-3690|
|Alice Brown|555-4321|

**Loan_Dates_3NF**


|Loan_ID|Loan_Date|Return_Date|
|---|---|---|
|1|2023-05-01|2023-06-01|
|2|2023-05-02|2023-06-02|
|3|2023-05-03|2023-06-03|
|4|2023-05-04|2023-06-04|

**Library_Branches_3NF**
(in bcnf)

| Branch_Name     | Branch_Street | Branch_City | Branch_State | Branch_Zip | Branch_Phone |
| --------------- | ------------- | ----------- | ------------ | ---------- | ------------ |
| Main Branch     | 123 Main St   | Cityville   | ST           | 12345      | 555-1111     |
| Downtown Branch | 456 Elm St    | Cityville   | ST           | 12345      | 555-2222     |
| Uptown Branch   | 789 Oak St    | Cityville   | ST           | 12345      | 555-3333     |

4. **Boyce Codd Normal Form (BCNF)** 
	* Assuming the table is in 3NF, **BCNF** is the next level of normalization that  looks at every single **determinant** (a column/set of columns that functionally determines a column/set of columns) and asks if *EVERY* determinant is a candidate key. 
	* If a *single* determinant isn't a candidate key, it's not in BCNF.
		* Reminder that **candidate keys** refers to keys that actually uniquely identify the entire row. 
		* If X is not a candidate key, then we need to take everything X -> Y (and any other candidates) and **make them into a separate table**
	* Example:
		* In the previous tables, **Library_Branches_3NF** is in BCNF since there's only one deteriminant, and that determinant is a key. **Loan_Dates_3NF** is not in **BCNF** because technically speaking, return date depends on loan date. And, **Borrowers_3NF** is obviously in **BCNF** since the only determinant is actually a candidate key.
	* 