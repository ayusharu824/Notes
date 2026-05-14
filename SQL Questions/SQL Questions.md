1. Second highest salary
## Table: Employee

| Id  | Name  | Salary |
| --- | ----- | ------ |
| 1   | John  | 50000  |
| 2   | Alice | 70000  |
| 3   | Bob   | 60000  |
| 4   | David | 70000  |

select top 1 salary from (select distinct top 2 salary from employee order by salary desc) as salary
order by salary asc

using CTE
 with CTE as 
 (
	 Select salary, DENSE_RANK() over (order by salary desc) as rn 
	 from employee
 )
 select salary from CTE where rn=2
 
2. Find duplicate emails
## Table: Users

|Id|Email|
|---|---|
|1|a@test.com|
|2|b@test.com|
|3|a@test.com|
|4|c@test.com|

---

# Question

Find all duplicate email addresses from the Users table.

Expected Output:

| Email      |
| ---------- |
| a@test.com |
select Email from Employee Group by Email having count(*) > 1;


# Question 3 — Employees Earning More Than Their Manager

## Table: Employee

| Id  | Name  | Salary | ManagerId |
| --- | ----- | ------ | --------- |
| 1   | John  | 50000  | NULL      |
| 2   | Alice | 70000  | 1         |
| 3   | Bob   | 40000  | 1         |
| 4   | David | 80000  | 2         |
select Name from Employee e
left join Employee m on e.Id =m.managerId

Try these using the tables above:

1. Find all employees in the **Engineering** department.
2. Find employees who earn **more than Alice**.
3. Find the **average salary** per department.
4. Find employees who have **never taken a leave**.
5. Find the **2nd highest salary**.
6. Find employees who earn **more than their manager**.


select emp_id, Name from employees e
inner join departments d on e.dept_id = d.dept_id
where e.dept_id = 101


select emp_id, name from employees where salary > (select salary from employees where emp_id = 1)


select avg(salary) AverageSalary, dept_name from employees e
left join departments d on e.dept_id = d.dept_id
group by dept_name


select e.emp_Id,name,l.emp_Id from employees e
left join leaves l on e.emp_Id=l.emp_Id
where l.emp_Id is null


select distinct top 1 salary from employees
where salary < (select max(salary) from employees)
order by salary desc

SELECT 
    e.emp_id,
    e.name AS EmpName,
    e.salary AS EmpSalary,
    m.emp_id AS ManagerID, 
    m.name AS ManagerName,
    m.salary AS ManagerSalary
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.emp_id
WHERE e.salary > m.salary;


# Customers With More Than 2 Orders

## Table: Orders

|OrderId|CustomerId|
|---|---|
|1|101|
|2|101|
|3|102|
|4|101|
|5|103|

---

## Question

Find customers who placed more than 2 orders.

select Count(CustomerId) 
from Orders
group by CustomerId
having count(CustomerId) > 1;

Latest Order Per Customer
|OrderId|CustomerId|OrderDate|
|---|---|---|
|1|101|2025-01-01|
|2|101|2025-01-10|
|3|102|2025-01-05|
|4|102|2025-01-08|

select CustomerId, Min(OrderDate) from orders
group by CustomerId


| Id  | Name  | ManagerId |
| --- | ----- | --------- |
| 1   | CEO   | NULL      |
| 2   | Alice | 1         |
| 3   | Bob   | 1         |
Find employees who do not have managers.

select Id, Name from Employee
where ManagerId is null


| Id  | Name  |
| --- | ----- |
| 1   | John  |
| 2   | John  |
| 3   | Alice |
| 4   | Bob   |
| 5   | Bob   |

select Name ,count(Name) from Users
group by Name
having count(Name) > 1

# 7. Top 2 Salaries Per Department

## Table: Employee

|Id|Name|Salary|Dept|
|---|---|---|---|
|1|John|90000|IT|
|2|Alice|85000|IT|
|3|Bob|80000|IT|
|4|David|95000|HR|
|5|Eva|92000|HR|

---

## Question

Find top 2 highest salaries from each department.


with cte as
(
  select Id, name, Salary, Dept, DENSE_RANK() over(partition by dept order by salary desc) as RN from Employee
)

select * from cte where rn<=2;


# 8. Find Missing Numbers

## Table: Numbers

|Num|
|---|
|1|
|2|
|3|
|5|
|6|
|9|

---

## Question

Find missing numbers.

leave it


# 9. Consecutive Login Days

## Table: Logins

| UserId | LoginDate  |
| ------ | ---------- |
| 1      | 2025-01-01 |
| 1      | 2025-01-02 |
| 1      | 2025-01-03 |
| 1      | 2025-01-05 |
| 2      | 2025-01-01 |
## Question

Find users who logged in for 3 consecutive days.

with CTE as (
  select UserId, LoginDate, 
  DateAdd(Day, - Row_number() 
  over(partition by userId order by LoginDate), LoginDate) Day from Logins 
)

SELECT UserId
FROM CTE
GROUP BY UserId, Day
HAVING COUNT(*) >= 3;


10. Display complete hierarchy using Recursive CTE.

select e.Id,e.Name EmpName, m.Id, m.Name Manager_Name
from Employee e
left join  Employee m on e.ManagerId = m.Id


# 12. Find Employees Joined in Same Month

## Table: Employee

| Id  | Name  | JoinDate   |
| --- | ----- | ---------- |
| 1   | John  | 2025-01-10 |
| 2   | Alice | 2025-01-15 |
| 3   | Bob   | 2025-02-01 |

with cte as 
(
  select *, count(*) over(partition by Year(JoinDate), Month(JoinDate)) as Dt from Employee
)
select * from cte where Dt > 1



# SQL Interview Revision Notes

### Quick revision guide — Questions & Answers

  

---

  

## 📋 Sample Tables Used Throughout

  

```sql

-- employees

CREATE TABLE employees (

  emp_id INT PRIMARY KEY, name VARCHAR(50),

  salary INT, dept_id INT, manager_id INT,

  hire_date DATE, city VARCHAR(50)

);

INSERT INTO employees VALUES

(1,'Alice',90000,101,NULL,'2019-03-15','Delhi'),

(2,'Bob',75000,102,1,'2020-06-01','Mumbai'),

(3,'Charlie',60000,101,1,'2021-01-10','Delhi'),

(4,'Diana',85000,103,1,'2018-11-20','Bangalore'),

(5,'Eve',70000,102,2,'2022-03-05','Mumbai'),

(6,'Frank',95000,103,4,'2017-07-19','Delhi'),

(7,'Grace',60000,101,1,'2023-09-01','Chennai'),

(8,'Hank',75000,102,2,'2020-06-01','Mumbai'),

(9,'Ivy',60000,103,4,'2021-04-22','Bangalore'),

(10,'John',50000,NULL,NULL,'2023-11-15','Delhi');

  

-- departments

CREATE TABLE departments (

  dept_id INT PRIMARY KEY, dept_name VARCHAR(50), location VARCHAR(50)

);

INSERT INTO departments VALUES

(101,'Engineering','Delhi'),(102,'Marketing','Mumbai'),

(103,'Finance','Bangalore'),(104,'HR','Chennai');

  

-- orders

CREATE TABLE orders (

  order_id INT PRIMARY KEY, customer_id VARCHAR(10),

  amount INT, order_date DATE

);

INSERT INTO orders VALUES

(1,'C001',1500,'2024-01-10'),(2,'C002',2300,'2024-01-15'),

(3,'C001',800,'2024-02-20'),(4,'C003',4500,'2024-03-05'),

(5,'C002',1200,'2024-04-18'),(6,'C001',3000,'2024-05-22'),

(7,'C003',700,'2024-06-30'),(8,'C004',2100,'2024-07-11'),

(9,'C002',500,'2024-08-09'),(10,'C001',1800,'2024-09-14');

  

-- leaves

CREATE TABLE leaves (

  leave_id INT PRIMARY KEY, emp_id INT, leave_date DATE, reason VARCHAR(50)

);

INSERT INTO leaves VALUES

(1,2,'2024-01-05','Sick'),(2,3,'2024-02-10','Personal'),

(3,5,'2024-03-15','Sick'),(4,2,'2024-04-20','Vacation'),

(5,7,'2024-05-25','Personal');

```

  

---

  

---

  

## SECTION 1 — JOINS

  

---

  

### Q1. Find all employees along with their department name. Include employees with no department.


**Concept:** LEFT JOIN — keeps all rows from left table even if no match on right.

  

```sql

SELECT e.emp_id, e.name, d.dept_name

FROM employees e

LEFT JOIN departments d ON e.dept_id = d.dept_id;

```

  

**Output:**

| name | dept_name |

|------|-----------|

| Alice | Engineering |

| Bob | Marketing |

| John | NULL |

  

**Key Point:** Use LEFT JOIN when you want ALL employees including those with no department. INNER JOIN would exclude John.

  

---

  

### Q2. Find departments that have NO employees.

  

**Concept:** LEFT JOIN + IS NULL trick to find non-matching rows.

  

```sql

SELECT d.dept_name

FROM departments d

LEFT JOIN employees e ON d.dept_id = e.dept_id

WHERE e.emp_id IS NULL;

```

  

**Output:**

| dept_name |

|-----------|

| HR |

  

**Key Point:** Start from departments (left), join employees (right). WHERE e.emp_id IS NULL means no employee matched that department.

  

---

  

### Q3. Find employees who earn more than their manager. (Self Join)

  

**Concept:** Self Join — join the same table twice, once as employee, once as manager.

  

```sql

SELECT e.name AS EmpName, e.salary AS EmpSalary,

       m.name AS ManagerName, m.salary AS ManagerSalary

FROM employees e

INNER JOIN employees m ON e.manager_id = m.emp_id

WHERE e.salary > m.salary;

```

  

**Output:**

| EmpName | EmpSalary | ManagerName | ManagerSalary |

|---------|-----------|-------------|---------------|

| Frank | 95000 | Diana | 85000 |

  

**Key Point:**

- `e` = employee copy, `m` = manager copy

- `e.manager_id = m.emp_id` is the bridge between them

- Use INNER JOIN — excludes Alice (no manager) automatically

- Use LEFT JOIN only if you want to include employees with no manager

  

---

  

### Q4. Find employees who have never taken a leave.

  

**Concept:** LEFT JOIN + IS NULL — find rows that have NO match in the right table.

  

```sql

SELECT e.emp_id, e.name

FROM employees e

LEFT JOIN leaves l ON e.emp_id = l.emp_id

WHERE l.emp_id IS NULL;

```

  

**Output:**

| name |

|------|

| Alice |

| Diana |

| Frank |

| Hank |

| Ivy |

| John |

  

**Key Point:**

- LEFT JOIN keeps all employees

- WHERE l.emp_id IS NULL means employee has NO leave record

- Do NOT select l.emp_id in SELECT — it will always be NULL here

  

---

  

### Q5. Find employees who work in the same city as their department location.

  

**Concept:** INNER JOIN with additional condition comparing two columns.

  

```sql

SELECT e.name, e.city, d.dept_name, d.location

FROM employees e

INNER JOIN departments d ON e.dept_id = d.dept_id

WHERE e.city = d.location;

```

  

**Output:**

| name | city | dept_name | location |

|------|------|-----------|----------|

| Alice | Delhi | Engineering | Delhi |

| Charlie | Delhi | Engineering | Delhi |

| Bob | Mumbai | Marketing | Mumbai |

| Eve | Mumbai | Marketing | Mumbai |

| Hank | Mumbai | Marketing | Mumbai |

| Diana | Bangalore | Finance | Bangalore |

| Ivy | Bangalore | Finance | Bangalore |

  

---

  

---

  

## SECTION 2 — AGGREGATIONS

  

---

  

### Q6. Find average salary per department.

  

**Concept:** GROUP BY + AVG + JOIN for dept name.

  

```sql

SELECT d.dept_name, AVG(e.salary) AS avg_salary

FROM employees e

LEFT JOIN departments d ON e.dept_id = d.dept_id

GROUP BY d.dept_id, d.dept_name;

```

  

**Output:**

| dept_name | avg_salary |

|-----------|------------|

| Engineering | 70000 |

| Marketing | 73333 |

| Finance | 80000 |

| NULL | 50000 |

  

**Key Point:** Always GROUP BY dept_id AND dept_name together — never just dept_name (two departments could have same name in different companies).

  

---

  

### Q7. Find departments where total salary exceeds 2,00,000.

  

**Concept:** HAVING — filter after grouping (cannot use WHERE with aggregate functions).

  

```sql

SELECT d.dept_name, SUM(e.salary) AS total_salary

FROM employees e

INNER JOIN departments d ON e.dept_id = d.dept_id

GROUP BY d.dept_id, d.dept_name

HAVING SUM(e.salary) > 200000;

```

  

**Output:**

| dept_name | total_salary |

|-----------|-------------|

| Engineering | 210000 |

| Marketing | 220000 |

| Finance | 240000 |

  

**Key Point:**

- WHERE filters rows BEFORE grouping

- HAVING filters groups AFTER grouping

- Never use SUM/AVG/COUNT in WHERE — always use HAVING

  

---

  

### Q8. Find customers who placed more than 2 orders.

  

**Concept:** GROUP BY + HAVING with COUNT.

  

```sql

SELECT customer_id, COUNT(*) AS total_orders

FROM orders

GROUP BY customer_id

HAVING COUNT(*) > 2;

```

  

**Output:**

| customer_id | total_orders |

|-------------|-------------|

| C001 | 4 |

| C002 | 3 |

  

---

  

### Q9. Find the month with the highest total order amount.

  

**Concept:** GROUP BY month + TOP 1 + ORDER BY.

  

```sql

-- Best version

SELECT TOP 1

    MONTH(order_date) AS month_number,

    FORMAT(order_date, 'MMM') AS month,

    SUM(amount) AS total_amount

FROM orders

GROUP BY MONTH(order_date), FORMAT(order_date, 'MMM')

ORDER BY SUM(amount) DESC;

```

  

**Output:**

| month_number | month | total_amount |

|-------------|-------|-------------|

| 3 | Mar | 4500 |

  

**Key Point:**

- Always GROUP BY month number (MONTH()) not just month name (FORMAT())

- Month names sort alphabetically — Apr comes before Jan alphabetically!

- Use MONTH() for correct chronological sorting

  

---

  

### Q10. Find customers who never ordered more than 2000 in a single order.

  

**Concept:** GROUP BY + HAVING MAX.

  

```sql

SELECT customer_id

FROM orders

GROUP BY customer_id

HAVING MAX(amount) <= 2000;

```

  

**Key Point:**

- `HAVING MAX(amount) <= 2000` means the biggest order that customer ever placed is still under 2000

- This is cleaner than using NOT IN or subqueries

  

---

  

---

  

## SECTION 3 — SUBQUERIES

  

---

  

### Q11. Fetch employees who earn more than Alice.

  

**Concept:** Scalar subquery — returns single value used in WHERE.

  

```sql

SELECT emp_id, name, salary

FROM employees

WHERE salary > (SELECT salary FROM employees WHERE emp_id = 1);

```

  

**Output:**

| name | salary |

|------|--------|

| Frank | 95000 |

  

**Key Point:** Subquery runs first → returns 90000 (Alice's salary) → outer query filters salary > 90000.

  

---

  

### Q12. Fetch employees who earn the maximum salary in their department.

  

**Concept:** Correlated subquery — subquery references outer query's column.

  

```sql

SELECT emp_id, name, salary, dept_id

FROM employees e

WHERE salary = (

    SELECT MAX(salary) FROM employees

    WHERE dept_id = e.dept_id

);

```

  

**Output:**

| name | salary | dept_id |

|------|--------|---------|

| Alice | 90000 | 101 |

| Bob | 75000 | 102 |

| Hank | 75000 | 102 |

| Frank | 95000 | 103 |

  

**Key Point:** Correlated subquery runs ONCE PER ROW — for each employee it finds the max salary of their specific department.

  

---

  

### Q13. Fetch employees who are also managers.

  

**Concept:** Subquery with IN — find emp_ids that appear as someone's manager_id.

  

```sql

SELECT emp_id, name

FROM employees

WHERE emp_id IN (

    SELECT DISTINCT manager_id FROM employees

    WHERE manager_id IS NOT NULL

);

```

  

**Output:**

| name |

|------|

| Alice |

| Bob |

| Diana |

  

---

  

### Q14. Find customers who ordered in August but NOT in September 2024.

  

**Concept:** NOT EXISTS — preferred over NOT IN for performance and NULL safety.

  

```sql

-- Method 1: NOT EXISTS (Best)

SELECT DISTINCT customer_id, MAX(order_date) AS last_order_date

FROM orders a

WHERE MONTH(order_date) = 8

AND YEAR(order_date) = 2024

AND NOT EXISTS (

    SELECT 1 FROM orders b

    WHERE b.customer_id = a.customer_id

    AND MONTH(b.order_date) = 9

    AND YEAR(b.order_date) = 2024

)

GROUP BY customer_id;

  

-- Method 2: LEFT JOIN

SELECT DISTINCT a.customer_id

FROM orders a

LEFT JOIN orders b

    ON a.customer_id = b.customer_id

    AND MONTH(b.order_date) = 9

    AND YEAR(b.order_date) = 2024

WHERE MONTH(a.order_date) = 8

AND YEAR(a.order_date) = 2024

AND b.customer_id IS NULL;

```

  

**Key Point:**

- NOT EXISTS stops searching as soon as it finds a match — faster on large data

- NOT IN fails if subquery returns any NULL values — avoid it

- LEFT JOIN + IS NULL and NOT EXISTS give same results

  

---

  

---

  

## SECTION 4 — WINDOW FUNCTIONS

  

---

  

### Q15. Find the 2nd highest salary.

  

**Concept:** DENSE_RANK window function or subquery approach.

  

```sql

-- Method 1: SQL Server (TOP)

SELECT DISTINCT TOP 1 salary

FROM employees

WHERE salary < (SELECT MAX(salary) FROM employees)

ORDER BY salary DESC;

  

-- Method 2: DENSE_RANK (works everywhere — best for interviews)

SELECT salary FROM (

    SELECT salary,

           DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk

    FROM employees

) t

WHERE rnk = 2;

```

  

**Output:**

| salary |

|--------|

| 90000 |

  

**Key Point:**

- DENSE_RANK — no gaps in ranking (1,2,2,3)

- RANK — has gaps (1,2,2,4)

- ROW_NUMBER — always unique (1,2,3,4)

- For Nth highest salary always use DENSE_RANK

  

---

  

### Q16. Find top 3 salaries per department.

  

**Concept:** DENSE_RANK with PARTITION BY.

  

```sql

SELECT * FROM (

    SELECT e.name, d.dept_name, e.salary,

           DENSE_RANK() OVER (

               PARTITION BY e.dept_id

               ORDER BY e.salary DESC

           ) AS rnk

    FROM employees e

    INNER JOIN departments d ON e.dept_id = d.dept_id

) t

WHERE rnk <= 3;

```

  

**Key Point:** PARTITION BY resets the rank for each department — like GROUP BY but for window functions.

  

---

  

### Q17. Find salary difference from highest paid in same department.

  

**Concept:** MAX() as window function with PARTITION BY.

  

```sql

-- Method 1: Window Function (Best — scans table once)

SELECT e.name, d.dept_name, e.salary,

       MAX(e.salary) OVER (PARTITION BY e.dept_id) AS dept_max,

       e.salary - MAX(e.salary) OVER (PARTITION BY e.dept_id) AS diff

FROM employees e

JOIN departments d ON e.dept_id = d.dept_id;

  

-- Method 2: Correlated Subquery (also correct)

SELECT e1.name,

       e1.salary,

       e1.salary - (SELECT MAX(salary) FROM employees e2

                    WHERE e2.dept_id = e1.dept_id) AS diff

FROM employees e1

WHERE e1.dept_id IS NOT NULL;

```

  

**Output:**

| name | dept_name | salary | dept_max | diff |

|------|-----------|--------|----------|------|

| Alice | Engineering | 90000 | 90000 | 0 |

| Charlie | Engineering | 60000 | 90000 | -30000 |

| Frank | Finance | 95000 | 95000 | 0 |

| Diana | Finance | 85000 | 95000 | -10000 |

  

**Key Point:** Window function is preferred — runs subquery once for all rows. Correlated subquery runs once PER ROW — slower on large tables.

  

---

  

### Q18. Find the second order placed by each customer.

  

**Concept:** ROW_NUMBER with PARTITION BY customer.

  

```sql

WITH ranked_orders AS (

    SELECT customer_id, order_id, amount, order_date,

           ROW_NUMBER() OVER (

               PARTITION BY customer_id

               ORDER BY order_date ASC

           ) AS rn

    FROM orders

)

SELECT customer_id, order_id, amount, order_date

FROM ranked_orders

WHERE rn = 2;

```

  

**Output:**

| customer_id | order_id | amount | order_date |

|-------------|----------|--------|------------|

| C001 | 3 | 800 | 2024-02-20 |

| C002 | 5 | 1200 | 2024-04-18 |

| C003 | 7 | 700 | 2024-06-30 |

  

**Key Point:**

- C004 has only 1 order — automatically excluded since no row has rn = 2

- Change WHERE rn = 2 to WHERE rn = N for Nth order

- Always use CTE + ROW_NUMBER — cleanest approach for interviews

  

---

  

### Q19. Calculate running total of order amounts per customer.

  

**Concept:** SUM() OVER with PARTITION BY and ORDER BY.

  

```sql

SELECT customer_id, order_date, amount,

       SUM(amount) OVER (

           PARTITION BY customer_id

           ORDER BY order_date ASC

       ) AS running_total

FROM orders;

```

  

**Output:**

| customer_id | order_date | amount | running_total |

|-------------|------------|--------|---------------|

| C001 | 2024-01-10 | 1500 | 1500 |

| C001 | 2024-02-20 | 800 | 2300 |

| C001 | 2024-05-22 | 3000 | 5300 |

| C001 | 2024-09-14 | 1800 | 7100 |

  

**Key Point:** PARTITION BY customer_id resets the running total for each new customer.

  

---

  

---

  

---

  

## SECTION 5 — WINDOW FUNCTIONS (Detailed)

  

---

  

### 🔑 Syntax of Every Window Function

  

```sql

FUNCTION_NAME() OVER (

    PARTITION BY column   -- like GROUP BY but doesn't collapse rows

    ORDER BY column       -- order within each partition

)

```

  

---

  

### W1. ROW_NUMBER — Unique row number, no ties

  

Assigns a unique number to every row. Even if salary is same, each gets a different number.

  

```sql

SELECT name, salary,

       ROW_NUMBER() OVER (ORDER BY salary DESC) AS row_num

FROM employees;

```

  

**Output:**

| name | salary | row_num |

|------|--------|---------|

| Frank | 95000 | 1 |

| Alice | 90000 | 2 |

| Diana | 85000 | 3 |

| Bob | 75000 | 4 |

| Hank | 75000 | 5 |

| Eve | 70000 | 6 |

| Charlie | 60000 | 7 |

| Grace | 60000 | 8 |

| Ivy | 60000 | 9 |

| John | 50000 | 10 |

  

**Use when:** You want exactly one row per group (e.g. second order per customer). Ties are broken arbitrarily.

  

---

  

### W2. RANK — Same rank for ties, skips next rank

  

```sql

SELECT name, salary,

       RANK() OVER (ORDER BY salary DESC) AS rnk

FROM employees;

```

  

**Output:**

| name | salary | rnk |

|------|--------|-----|

| Frank | 95000 | 1 |

| Alice | 90000 | 2 |

| Diana | 85000 | 3 |

| Bob | 75000 | 4 |

| Hank | 75000 | 4 |

| Eve | 70000 | 6 |← skips 5!

| Charlie | 60000 | 7 |

| Grace | 60000 | 7 |

| Ivy | 60000 | 7 |

| John | 50000 | 10 |← skips 8,9!

  

**Use when:** Sports ranking — 2nd place tie means next person is 4th, not 3rd.

  

---

  

### W3. DENSE_RANK — Same rank for ties, NO gaps

  

```sql

SELECT name, salary,

       DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk

FROM employees;

```

  

**Output:**

| name | salary | rnk |

|------|--------|-----|

| Frank | 95000 | 1 |

| Alice | 90000 | 2 |

| Diana | 85000 | 3 |

| Bob | 75000 | 4 |

| Hank | 75000 | 4 |

| Eve | 70000 | 5 |← no gap!

| Charlie | 60000 | 6 |

| Grace | 60000 | 6 |

| Ivy | 60000 | 6 |

| John | 50000 | 7 |← no gap!

  

**Use when:** Finding Nth highest salary — always use DENSE_RANK ✅

  

```sql

-- Find 2nd highest salary

SELECT salary FROM (

    SELECT salary,

           DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk

    FROM employees

) t

WHERE rnk = 2;

-- Output: 90000 (Alice)

```

  

---

  

### W4. ROW_NUMBER vs RANK vs DENSE_RANK — Side by Side

  

| name | salary | ROW_NUMBER | RANK | DENSE_RANK |

|------|--------|-----------|------|------------|

| Frank | 95000 | 1 | 1 | 1 |

| Alice | 90000 | 2 | 2 | 2 |

| Diana | 85000 | 3 | 3 | 3 |

| Bob | 75000 | 4 | 4 | 4 |

| Hank | 75000 | 5 | 4 | 4 |← same salary

| Eve | 70000 | 6 | 6 | 5 |← RANK skips 5, DENSE_RANK doesn't

| Charlie | 60000 | 7 | 7 | 6 |

| Grace | 60000 | 8 | 7 | 6 |← same salary

| Ivy | 60000 | 9 | 7 | 6 |← same salary

| John | 50000 | 10 | 10 | 7 |

  

**Key Rule:**

- ROW_NUMBER → always unique (1,2,3,4,5,6...)

- RANK → ties same, skips after (1,2,3,4,4,6...)

- DENSE_RANK → ties same, no skips (1,2,3,4,4,5...)

  

---

  

### W5. PARTITION BY — Rank within each group

  

```sql

-- Rank employees within each department by salary

SELECT name, dept_id, salary,

       DENSE_RANK() OVER (

           PARTITION BY dept_id

           ORDER BY salary DESC

       ) AS dept_rank

FROM employees

WHERE dept_id IS NOT NULL;

```

  

**Output:**

| name | dept_id | salary | dept_rank |

|------|---------|--------|-----------|

| Alice | 101 | 90000 | 1 |

| Charlie | 101 | 60000 | 2 |

| Grace | 101 | 60000 | 2 |

| Hank | 102 | 75000 | 1 |

| Bob | 102 | 75000 | 1 |

| Eve | 102 | 70000 | 2 |

| Frank | 103 | 95000 | 1 |

| Diana | 103 | 85000 | 2 |

| Ivy | 103 | 60000 | 3 |

  

**Key Point:** PARTITION BY resets rank counter for each department. Without PARTITION BY it ranks across the whole company.

  

---

  

### W6. LAG — Get value from PREVIOUS row

  

```sql

SELECT customer_id, order_date, amount,

       LAG(amount) OVER (

           PARTITION BY customer_id

           ORDER BY order_date

       ) AS prev_order_amount,

       amount - LAG(amount) OVER (

           PARTITION BY customer_id

           ORDER BY order_date

       ) AS diff_from_prev

FROM orders;

```

  

**Output:**

| customer_id | order_date | amount | prev_order_amount | diff_from_prev |

|-------------|------------|--------|-------------------|----------------|

| C001 | 2024-01-10 | 1500 | NULL | NULL |← first order, no previous

| C001 | 2024-02-20 | 800 | 1500 | -700 |

| C001 | 2024-05-22 | 3000 | 800 | +2200 |

| C001 | 2024-09-14 | 1800 | 3000 | -1200 |

| C002 | 2024-01-15 | 2300 | NULL | NULL |← resets for new customer

| C002 | 2024-04-18 | 1200 | 2300 | -1100 |

  

**Use when:** Compare current row with previous row — month over month growth, duplicate detection.

  

**LAG syntax:**

```sql

LAG(column, offset, default_value) OVER (...)

-- offset = how many rows back (default 1)

-- default_value = what to show when no previous row exists (default NULL)

  

LAG(amount, 1, 0)  -- go 1 row back, show 0 if no previous row

LAG(amount, 2)     -- go 2 rows back

```

  

---

  

### W7. LEAD — Get value from NEXT row

  

```sql

SELECT customer_id, order_date, amount,

       LEAD(amount) OVER (

           PARTITION BY customer_id

           ORDER BY order_date

       ) AS next_order_amount

FROM orders;

```

  

**Output:**

| customer_id | order_date | amount | next_order_amount |

|-------------|------------|--------|-------------------|

| C001 | 2024-01-10 | 1500 | 800 |

| C001 | 2024-02-20 | 800 | 3000 |

| C001 | 2024-05-22 | 3000 | 1800 |

| C001 | 2024-09-14 | 1800 | NULL |← last order, no next

  

**Use when:** Find next event, check if customer ordered again, detect last row.

  

---

  

### W8. SUM OVER — Running Total

  

```sql

SELECT customer_id, order_date, amount,

       SUM(amount) OVER (

           PARTITION BY customer_id

           ORDER BY order_date

       ) AS running_total

FROM orders;

```

  

**Output:**

| customer_id | order_date | amount | running_total |

|-------------|------------|--------|---------------|

| C001 | 2024-01-10 | 1500 | 1500 |

| C001 | 2024-02-20 | 800 | 2300 |

| C001 | 2024-05-22 | 3000 | 5300 |

| C001 | 2024-09-14 | 1800 | 7100 |

| C002 | 2024-01-15 | 2300 | 2300 |← resets for C002

| C002 | 2024-04-18 | 1200 | 3500 |

  

---

  

### W9. AVG OVER — Compare each row to group average

  

```sql

SELECT name, dept_id, salary,

       AVG(salary) OVER (PARTITION BY dept_id) AS dept_avg,

       salary - AVG(salary) OVER (PARTITION BY dept_id) AS diff_from_avg

FROM employees

WHERE dept_id IS NOT NULL;

```

  

**Output:**

| name | dept_id | salary | dept_avg | diff_from_avg |

|------|---------|--------|----------|---------------|

| Alice | 101 | 90000 | 70000 | +20000 |

| Charlie | 101 | 60000 | 70000 | -10000 |

| Grace | 101 | 60000 | 70000 | -10000 |

| Bob | 102 | 75000 | 73333 | +1667 |

| Eve | 102 | 70000 | 73333 | -3333 |

  

---

  

### W10. PERCENT_RANK — Salary percentile

  

```sql

SELECT name, salary,

       CAST(PERCENT_RANK() OVER (ORDER BY salary) * 100 AS INT) AS percentile

FROM employees;

```

  

**Output:**

| name | salary | percentile |

|------|--------|------------|

| John | 50000 | 0% |

| Charlie | 60000 | 11% |

| Grace | 60000 | 11% |

| Ivy | 60000 | 11% |

| Eve | 70000 | 44% |

| Bob | 75000 | 55% |

| Hank | 75000 | 55% |

| Diana | 85000 | 77% |

| Alice | 90000 | 88% |

| Frank | 95000 | 100% |

  

---

  

### ⚡ Window Functions Quick Reference

  

| Function | What it does | Common use |

|----------|-------------|------------|

| ROW_NUMBER() | Unique number per row | Get Nth row per group |

| RANK() | Rank with gaps on ties | Sports rankings |

| DENSE_RANK() | Rank without gaps on ties | Nth highest salary ✅ |

| LAG(col, n) | Value from n rows BEFORE | Month over month comparison |

| LEAD(col, n) | Value from n rows AFTER | Next event detection |

| SUM() OVER | Running total | Cumulative revenue |

| AVG() OVER | Group average per row | Compare to dept average |

| MAX() OVER | Group max per row | Diff from highest in group |

| PERCENT_RANK() | Percentile rank 0 to 1 | Salary percentile |

| COUNT() OVER | Running count | Cumulative customer count |

  

---

  

## SECTION 5 — SELF JOIN

  

---

  

### Q20. Find pairs of employees in the same department.

  

**Concept:** Self Join with emp_id < emp_id to avoid duplicate pairs.

  

```sql

SELECT a.name AS Employee1, b.name AS Employee2, a.dept_id

FROM employees a

INNER JOIN employees b

    ON a.dept_id = b.dept_id

    AND a.emp_id < b.emp_id;

```

  

**Key Point:** `a.emp_id < b.emp_id` prevents showing (Alice, Bob) AND (Bob, Alice) — shows each pair only once.

  

---

  

---

  

## SECTION 6 — CTEs

  

---

  

### Q21. Find employees whose salary is above company average but below department max.

  

**Concept:** CTE to calculate metrics separately then join.

  

```sql

WITH company_avg AS (

    SELECT AVG(salary) AS avg_sal FROM employees

),

dept_max AS (

    SELECT dept_id, MAX(salary) AS max_sal

    FROM employees

    GROUP BY dept_id

)

SELECT e.name, e.salary,

       ca.avg_sal AS company_avg,

       dm.max_sal AS dept_max

FROM employees e

JOIN dept_max dm ON e.dept_id = dm.dept_id

JOIN company_avg ca ON 1 = 1

WHERE e.salary > ca.avg_sal

AND   e.salary < dm.max_sal;

```

  

**Output:**

| name | salary | company_avg | dept_max |

|------|--------|-------------|----------|

| Diana | 85000 | 72000 | 95000 |

  

**Key Point:** CTEs make complex queries readable — define once, use multiple times. Preferred over nested subqueries in interviews.

  

---

  

---

  

## SECTION 7 — DATE QUERIES

  

---

  

### Q22. Find employees hired in the last 3 years.

  

```sql

SELECT name, hire_date

FROM employees

WHERE hire_date >= DATEADD(YEAR, -3, GETDATE());

```

  

---

  

### Q23. Find how many employees joined each year.

  

```sql

SELECT YEAR(hire_date) AS hire_year, COUNT(*) AS total

FROM employees

GROUP BY YEAR(hire_date)

ORDER BY hire_year;

```

  

---

  

### Q24. Calculate years of experience for each employee.

  

```sql

SELECT name, hire_date,

       DATEDIFF(YEAR, hire_date, GETDATE()) AS years_experience

FROM employees;

```

  

---

  

---

  

## SECTION 8 — DUPLICATE HANDLING

  

---

  

### Q25. Find duplicate records in salaries table.

  

```sql

SELECT emp_id, amount, COUNT(*) AS cnt

FROM salaries

GROUP BY emp_id, amount

HAVING COUNT(*) > 1;

```

  

---

  

### Q26. Delete duplicates — keep only the latest (highest id).

  

```sql

DELETE FROM salaries

WHERE id NOT IN (

    SELECT MAX(id)

    FROM salaries

    GROUP BY emp_id, amount

);

```

  

**Key Point:** Always keep one row — use MAX(id) to keep latest, MIN(id) to keep oldest.

  

---

  

---

  

## SECTION 9 — VIEWS

  

---

  

### Q27. Create a view to show employee details with department.

  

```sql

-- Create

CREATE VIEW emp_details AS

SELECT e.emp_id, e.name, e.salary, d.dept_name, e.city

FROM employees e

JOIN departments d ON e.dept_id = d.dept_id;

  

-- Use like a table

SELECT * FROM emp_details WHERE city = 'Delhi';

  

-- Join with other tables

SELECT v.name, SUM(o.amount)

FROM emp_details v

JOIN orders o ON v.emp_id = o.customer_id

GROUP BY v.name;

  

-- Drop

DROP VIEW emp_details;

```

  

**Key Point:**

- View = saved query, NOT stored data

- Always reflects latest data automatically

- Can be used in SELECT, JOIN, WHERE — unlike stored procedures

- Use for security (hide salary column), simplicity, reusability

  

---

  

---

  

## SECTION 10 — TRANSACTIONS

  

---

  

### Q28. Transfer money between two accounts safely.

  

```sql

BEGIN TRANSACTION

BEGIN TRY

  

    UPDATE accounts SET balance = balance - 10000 WHERE acc_id = 1;

    UPDATE accounts SET balance = balance + 10000 WHERE acc_id = 2;

  

    COMMIT;

    PRINT 'Transfer successful!';

  

END TRY

BEGIN CATCH

  

    ROLLBACK;

    PRINT 'Transfer failed! ' + ERROR_MESSAGE();

  

END CATCH

```

  

**Key Point:**

- BEGIN TRY → COMMIT → success path

- BEGIN CATCH → ROLLBACK → failure path

- Either BOTH updates happen or NEITHER — no partial updates

  

---

  

---

  

## ⚡ Quick Reference Cheatsheet

  

### When to use what

  

| Scenario | Use |

|----------|-----|

| Filter individual rows | WHERE |

| Filter after grouping | HAVING |

| All rows from left table | LEFT JOIN |

| Only matching rows | INNER JOIN |

| Same table comparison | SELF JOIN |

| Rank within groups | DENSE_RANK + PARTITION BY |

| Running total | SUM() OVER (ORDER BY) |

| Previous row value | LAG() |

| Next row value | LEAD() |

| Clean complex queries | CTE (WITH clause) |

| Reusable query as table | VIEW |

| All or nothing updates | TRANSACTION |

  

---

  

### JOIN vs LEFT JOIN

  

| | INNER JOIN | LEFT JOIN |

|--|------------|-----------|

| No match rows | Excluded | Included as NULL |

| Use when | You need matching rows only | You need ALL rows from left |

  

---

  

### WHERE vs HAVING

  

| | WHERE | HAVING |

|--|-------|--------|

| When it runs | Before GROUP BY | After GROUP BY |

| Can use aggregates | ❌ No | ✅ Yes |

| Example | WHERE salary > 5000 | HAVING AVG(salary) > 5000 |

  

---

  

### ROW_NUMBER vs RANK vs DENSE_RANK

  

| Salaries | ROW_NUMBER | RANK | DENSE_RANK |

|----------|-----------|------|------------|

| 95000 | 1 | 1 | 1 |

| 90000 | 2 | 2 | 2 |

| 85000 | 3 | 3 | 3 |

| 75000 | 4 | 4 | 4 |

| 75000 | 5 | 4 | 4 |

| 70000 | 6 | 6 | 5 |

  

- ROW_NUMBER → always unique, no ties

- RANK → ties get same rank, next rank skips

- DENSE_RANK → ties get same rank, no gaps ✅ use for Nth highest

  

---

  

### NOT IN vs NOT EXISTS vs LEFT JOIN IS NULL

  

| | NOT IN | NOT EXISTS | LEFT JOIN IS NULL |

|--|--------|-----------|-------------------|

| NULL safe | ❌ No | ✅ Yes | ✅ Yes |

| Performance | Slowest | Fastest | Fast |

| Recommended | Avoid | ✅ Best | Good |

  

---

  

### View vs Stored Procedure

  

| | View | Stored Procedure |

|--|------|-----------------|

| Use in SELECT/JOIN | ✅ Yes | ❌ No |

| Has IF/ELSE logic | ❌ No | ✅ Yes |

| INSERT/UPDATE/DELETE | ⚠️ Limited | ✅ Yes |

| Best for | Hiding columns, reusable queries | Business logic, data modification |

  

---

  

### ACID Properties (Transaction)

  

| Property | Meaning |

|----------|---------|

| Atomicity | All or nothing |

| Consistency | Data always valid |

| Isolation | Transactions don't interfere |

| Durability | Committed = permanent |

  

---

  

## 🧠 Top Interview Tips for 7 Years Exp

  

1. Always mention **indexes** when discussing performance

2. Prefer **CTEs** over nested subqueries — more readable

3. Use **window functions** over correlated subqueries — more efficient

4. Always handle **NULL values** — mention edge cases

5. Use **NOT EXISTS** instead of **NOT IN** — NULL safe and faster

6. For Nth highest salary always use **DENSE_RANK**

7. Always **GROUP BY** on id + name together, not just name

8. Explain your **thought process** out loud in interviews

9. Mention **execution plan** when asked about slow queries

10. Know the difference between **RANK, DENSE_RANK, ROW_NUMBER**
