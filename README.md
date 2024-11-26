# Library SQL Coding

#**Project Overview**
#**Project Title:** Library Management System
#**Level:** Intermediate
#**Database:** library_db

This project demonstrates the implementation of a Library Management System using SQL. It includes creating and managing tables, performing CRUD operations, and executing advanced SQL queries. The goal is to showcase skills in database design, manipulation, and querying.


##**Objectives**
**Set up the Library Management System Database:** Create and populate the database with tables for branches, employees, members, books, issued status, and return status.
**CRUD Operations:** Perform Create, Read, Update, and Delete operations on the data.
**CTAS (Create Table As Select):** Utilize CTAS to create new tables based on query results.


**Database Creation:** Created a database named library_db.
**Table Creation:** Created tables for branches, employees, members, books, issued status, and return status. Each table includes relevant columns and relationships.


```sql
select *
from books;
```
select *
from branch;

select *
from employees;

select *
from issued_status;

select *
from return_status;


---Project Tasks and Business Problem
--Task 1. Create a New Book Record -- "978-1-60129-456-2', 
--'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"

insert into books(isbn, book_title, category, rental_price, status, author, publisher)
values
('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')

select * from books;


--Task 2: Update an Existing Member's Address

UPDATE members
SET member_address = '125 Oak St'
WHERE member_id = 'C103';


--Task 3: Delete a Record from the Issued Status Table -- 
--Objective: Delete the record with issued_id = 'IS121' from the issued_status table.
DELETE FROM issued_status
WHERE   issued_id =   'IS121';

--Task 4: Retrieve All Books Issued by a Specific Employee 
-- Objective: Select all books issued by the employee with emp_id = 'E101'.

select *
from issued_status
where issued_emp_id = 'E101';

--Task 5: List Members Who Have Issued More Than One Book
-- Objective: Use GROUP BY to find members who have issued more than one book.

select issued_emp_id, count(issued_id) as Total_Book_Issued
from issued_status
group by 1
having count(issued_id) > 1;


--CTAS
--Task 6: Create Summary Tables: 
--Used CTAS to generate new tables based on query results - each book and total book_issued_cnt**
create table book_counts
as
(select b.book_title, b.isbn, count(*)/count(ist.issued_id) as issued_number
from books as b
inner join issued_status as ist
on ist.issued_book_isbn = b.isbn
group by 1,2);


select * from book_counts;

--Task 7. Retrieve All Books in a Specific Category
select *
from books
where category = 'Classic';

--Task 8: Find Total Rental Income by Category:
select b.Category, sum(b.rental_price), count(*)
from books as b
inner join issued_status as ist
on ist.issued_book_isbn = b.isbn
group by 1;

--Task 9. List Members Who Registered in the Last 180 Days


select *
from members
where reg_date >= current_Date - Interval '180 days';

--Task 10. List Employees with Their Branch Manager's Name and their branch details

select e1.*, b.manager_id, e2.emp_id as manager, b.branch_id
from employees as e1
join branch as b
using(branch_id)
join employees as e2
on b.manager_id = e2.emp_id;

-- Task 11. Create a Table of Books with Rental Price Above a Certain Threshold 7USD
create table books_above_7
as
(select *
from books
where rental_price > 7.0);

select * from books_above_7

--Task 12. Retrieve the List of Books Not Yet Returned

select distinct(ist.issued_book_name)
from issued_status as ist
left join return_status as rs
using(issued_id)
where return_id is null;
