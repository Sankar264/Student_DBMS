# Student DBMS — Student Database Management System

## What is this project?

This is a mini project where I built a **database for a college** using
MySQL. It stores information about students, faculty, departments, courses,
who is enrolled in which course, who teaches which course, and each
student's guardian and phone number details.

Basically, I took a real problem — a college keeping student data in
scattered Excel sheets and registers — and solved it by designing a proper
database with tables, relationships, and 40 SQL queries to fetch useful
information out of it.

---

## Why I built this

In a college, the same questions come up again and again:

- Which students are in the CS department?
- Who scored an A grade in Database Systems?
- Who is the guardian of student number 5?
- How many courses does a faculty member teach?

If all this data is kept in separate files, finding answers is slow and
mistakes happen easily (like the same student's department written
differently in two places). A database fixes this by keeping everything
connected in one place, so any of these questions can be answered with
one SQL query.

This project shows how ER (Entity-Relationship) modeling — the theory part
(entities, attributes, relationships, strong/weak entities) — is actually
turned into real tables and real queries.

---

## Files in this project

| File | What it has |
|---|---|
| `student_database.sql` | Creates the `student_dbms` database, all 8 tables, sample data, and 40 solved queries |
| `README.md` | This file — explains the whole project |

Just run the `.sql` file and you get a ready database filled with sample
data: 5 departments, 8 faculty, 15 students, 10 courses, and 25 course
enrollments.

---

## Tables (Entities) used

| Table | What it stores | Type |
|---|---|---|
| `Department` | Department details (CS, Mechanical, etc.) | Strong entity |
| `Faculty` | Teacher/staff details | Strong entity |
| `Student` | Student details | Strong entity |
| `Course` | Course details | Strong entity |
| `Guardian` | Student's parent/guardian info | Weak entity |
| `StudentPhone` | Student's phone number(s) | Weak entity |
| `Enrollment` | Which student took which course, and their grade | Bridge table (many-to-many) |
| `Teaches` | Which faculty teaches which course | Bridge table (many-to-many) |

### Strong entity vs weak entity — in simple words

A **strong entity** can stand on its own. A `Student` row makes sense by
itself with its own ID (`StudentID`).

A **weak entity** cannot stand on its own — it only makes sense if it
belongs to another table. A `Guardian` row means nothing unless you know
*which student* it belongs to. So a guardian is identified using a
`GuardianID` **plus** the `StudentID` together — not `GuardianID` alone.
If a student is deleted, their guardians and phone numbers get deleted
automatically too (`ON DELETE CASCADE`), so no leftover junk data stays in
the database.

### Why Enrollment and Teaches tables are needed

One student can take many courses, and one course can have many students.
This is called a **many-to-many relationship**, and you cannot show it with
a normal foreign key. So we create a separate table (`Enrollment`) in the
middle that connects `Student` and `Course`. The same logic applies to
`Teaches`, which connects `Faculty` and `Course`.

The `Grade` column also makes more sense here — a grade belongs to *one
student's enrollment in one course*, not to the student alone or the
course alone.

---

## How the tables are connected (relationships)

```
Department ──< Faculty ─────┐
    │  │                    │
    │  └─(1:1 Head of Dept) │
    │                       ├──< Teaches >── Course
    ├──< Student            │                  │
    │       │               └──< Enrollment >──┘
    │       ├──< Guardian
    │       └──< StudentPhone
    └──< Course
```

- One Department has many Faculty, many Students, and many Courses (1:N)
- One Faculty member is the Head of exactly one Department (1:1)
- Student and Course are connected many-to-many through Enrollment
- Faculty and Course are connected many-to-many through Teaches
- One Student can have many Guardians and many Phone numbers (1:N)

---

## Attributes used, with examples

| Attribute type | Meaning in simple words | Example |
|---|---|---|
| Simple | Just one value, can't be broken down | `Course.Credits` |
| Composite | Made up of smaller parts | Student's name (First name + Last name) |
| Key | Unique value that identifies one row | `StudentID`, `CourseID` |
| Partial key | Only identifies a row along with the owner's key | `GuardianID` (needs `StudentID` too) |
| Multivalued | Can have more than one value | A student can have more than one phone number, so it's stored in a separate `StudentPhone` table instead of one column |
| Derived | Not stored, calculated when needed | `Age` — calculated from `DOB` every time, instead of storing a value that goes out of date |

---

## What kind of data can I pull out of this database?

The 40 queries cover pretty much every type of question a college office
would actually ask:

- Simple lookups — students in a department, who's enrolled in a course
- Counting and totals — students per department, total credits per student
- Comparisons — top scorer in a course, students older than the average age
- Joining many tables together — faculty with their department and courses
- Checking missing data — students with no guardian, courses with no faculty
- Grouped summaries — male vs female count, guardian occupation count
- Changing data safely — updating a grade, deleting a record, and doing a
  transaction (commit) so that a multi-step change either fully happens or
  doesn't happen at all
- A saved view (`StudentReport`) — a ready-made report so you don't have to
  write the same join again and again

Every query in the file was actually tested on the sample data before being
added, so what's written in the comment below each query is the real
answer, not a guess.

---

## How to run this project

```bash
mysql -u root -p < student_database.sql
```

This will create the `student_dbms` database, fill it with sample data, and
run all 40 queries one after another. A few queries (update grade, delete
guardian, add new enrollment) will actually change the sample data — that's
done on purpose, to show real INSERT/UPDATE/DELETE working, not just
SELECT.

---

## What I learned from this project

- How to tell apart a **strong entity** and a **weak entity**, and why a
  weak entity needs a partial key plus the owner's key together.
- Why **many-to-many relationships** need a separate bridge table
  (`Enrollment`, `Teaches`) instead of just adding a foreign key column.
- Why some attributes should not be stored directly — like `Age`, which
  should always be calculated from `DOB`, or phone numbers, which should
  go in their own table instead of one column, since a student can have
  more than one.
- How **JOIN**, **GROUP BY**, **subqueries**, **views**, and
  **transactions** are used to actually get useful answers out of a
  database, not just store data.
- How normalization (keeping data non-repeated and organized properly)
  avoids messy, duplicate, or wrong data as the database grows bigger.
- Most importantly — how ER diagram theory (entities, attributes, weak vs
  strong, cardinality) that we study on paper actually becomes real tables
  and real working SQL queries.

---

## All 40 questions solved, with the topic each one covers

1. Find all students in the Computer Science department *(SELECT + JOIN + WHERE)*
2. Show full name and age of every student *(String concat + Derived attribute)*
3. Find students who haven't enrolled in any course *(NOT IN subquery)*
4. Count of students in each department *(GROUP BY + COUNT + LEFT JOIN)*
5. Departments with more than 2 students *(GROUP BY + HAVING)*
6. List all courses offered by each department *(JOIN + ORDER BY)*
7. Faculty who teach more than 1 course *(GROUP BY + HAVING + COUNT DISTINCT)*
8. Students enrolled in 'Database Systems' *(Multi-table JOIN)*
9. Guardians of a particular student *(JOIN on weak entity)*
10. All phone numbers of each student *(Multivalued attribute + GROUP_CONCAT)*
11. Total credits each student is enrolled for *(SUM + GROUP BY)*
12. Students who scored an A-range grade *(LIKE pattern matching)*
13. Highest scorer in each course *(Window function — RANK)*
14. Department-wise average enrollments per student *(Aggregate with JOIN)*
15. Faculty who is Head of Department *(1:1 relationship JOIN)*
16. Courses with no faculty assigned *(NOT IN subquery / anti-join)*
17. Students admitted in the year 2022 *(YEAR date function)*
18. Youngest and oldest student *(Subquery with MIN/MAX)*
19. Students who live in the same city *(Self-join)*
20. Faculty along with their department *(Basic INNER JOIN)*
21. Students who scored at least one A+ *(EXISTS — correlated subquery)*
22. Combined list of department names and course names *(UNION)*
23. Count of male vs female students *(GROUP BY single column)*
24. Students with no guardian added *(LEFT JOIN + IS NULL)*
25. Course with the highest number of enrollments *(GROUP BY + ORDER BY + LIMIT)*
26. Faculty hired before the year 2016 *(Date filtering)*
27. Department offering the most courses *(GROUP BY + ORDER BY + LIMIT)*
28. Students with more than 2 enrollments *(Common Table Expression — WITH)*
29. Update a student's grade *(UPDATE — DML)*
30. Delete a guardian record *(DELETE — DML)*
31. Create a reusable student report-card view *(CREATE VIEW)*
32. Rank students by number of courses taken *(Window function — ROW_NUMBER)*
33. Departments with no HOD assigned *(IS NULL check)*
34. Students older than the average student age *(Nested subquery)*
35. Every course with its faculty and department *(Multi-table JOIN, no grouping)*
36. Courses that carry more than 3 credits *(Simple WHERE filter)*
37. Students enrolled in both Database Systems and Data Structures *(Multi-join — same table joined twice)*
38. Total credits offered by each department *(SUM + GROUP BY)*
39. Guardian occupation distribution *(GROUP BY + COUNT)*
40. Safely enroll a student in a course *(TRANSACTION — START TRANSACTION / COMMIT)*
