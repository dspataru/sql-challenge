# sql-challenge: Employee database analysis using SQL

## Table of Contents
* [Background](https://github.com/dspataru/sql-challenge/blob/main/README.md#background)
* [Data Modeling: Creating an ERD](https://github.com/dspataru/sql-challenge/blob/main/README.md#data-modeling-creating-an-erd)
* [Data Engineering: Creating Table Schemas and Importing the CSV Files](https://github.com/dspataru/sql-challenge/blob/main/README.md#data-engineering-creating-table-schemas-and-importing-the-csv-files)
* [Data Analysis: Querying the Tables](https://github.com/dspataru/sql-challenge/blob/main/README.md#data-analysis-querying-the-tables)

## Background

For this project we are acting as a new data engineer at a fictional company (Pewlett Hackard) with the task to review the database of people whom were employed by the company between the 1980s to the 1990s. There are six CSV files that remained as part of the employee database from that period that are used to perform data analysis on through queries in SQL.

This repository contains SQL files for designing and querying tables that hold data from CSV files. The CSV files were imported into an SQL database and queried. The following CSV files were imported into pgAdmin 4:
1. [departments.csv](https://github.com/dspataru/sql-challenge/blob/main/EmployeeSQL/data/departments.csv) : table containing two columns: (1) department number from d001 to d009, and (2) department name associated with the department number. The different departments are: marketing, finance, human resources, production, development, quality management, sales, research, and customer service.
2. [dept_emp.csv](https://github.com/dspataru/sql-challenge/blob/main/EmployeeSQL/data/dept_emp.csv) : this table contains two columns: (1) employee number which is unique, and (2) department number from d001 to d009. This table is used to associate each employee in the company with the department they work in.
3. [dept_manager.csv](https://github.com/dspataru/sql-challenge/blob/main/EmployeeSQL/data/dept_manager.csv) : this table contains two columns: (1) department number from d001 to d009, and (2) employee number of managers in each department. This table is used to show how many managers each department has, and which employees are managers of which department.
4. [titles.csv](https://github.com/dspataru/sql-challenge/blob/main/EmployeeSQL/data/titles.csv) : this table contains two columns: (1) a unique title id that is associated with a (2) title name. The different title names that can be assigned to employees at the company are staff, senior staff, assistant engineer, engineer, senior engineer, technique leader, and manager.
5. [employees.csv](https://github.com/dspataru/sql-challenge/blob/main/EmployeeSQL/data/employees.csv) : this table contains detailed information about each employee in the company between the 1980s to the 1990s. There are seven columns: (1) employee number that is unique to each employee, (2) employee title that is found in the title id column in the titles.csv table, (3) birth date of each employee, (4) first and (5) last name of each employee, (6) sex (M/F) of each employee, and lastly, (7) the hire date of each employee.
6. [salaries.csv](https://github.com/dspataru/sql-challenge/blob/main/EmployeeSQL/data/salaries.csv) : this table contains the information about the salary for each employee in the company. The table contains two columns: (1) employee number which is unique, and (2) the salary associated with an employee id.

The project is divided into three parts:
1. Data modeling
2. Data engineering
3. Data analysis

#### Key Words
SQL, queries, pgAdmin 4, Entity Relationship Diagram (ERD), data analysis, database, data modelling, data engineering, QuickDBD

## Data Modeling: Creating an ERD

All CSV files listed in the Background section were inspected to understand what the columns are, and what information could be accessed from each of the tables individually. In order to visualize the relationships between the entities in each table, and ERD was created by defining the entities, their attributes, and showing the relationships between them to understand the logical structure of the database. The tool that was used to construct the ERD was [QuickDBD](https://app.quickdatabasediagrams.com/#/). The code that was used to develop the ERD in QuickDBD can be found in the [ERD_SQL-challenge.txt](https://github.com/dspataru/sql-challenge/blob/main/EmployeeSQL/ERD_SQL-challenge.txt) file in the main folder of this repository. The output of the diagram is seen below.

![ERD](https://github.com/dspataru/sql-challenge/blob/main/EmployeeSQL/images/employees_ERD.png)

## Data Engineering: Creating Table Schemas and Importing the CSV Files

After the ERD was created, the next step was to create a table schema for each of the six CSV files. A few important items were taken into consideration when constructing the schemas in pgAdmin.
1. Specifying the data types (int, varchar, etc), primary keys, foreign keys, and other constraints.
2. When assigning the primary key, it is important to assign based on the unique column. Otherwise, a composite key can be created which takes two primary keys to uniquely identify a row.
3. The order in which the tables are created in pgAdmin are important so the foreign keys are handled correctly.

Below shows the order in which the tables needed to be created:
```sql
CREATE TABLE titles (
	title_id VARCHAR(5) NOT NULL,
	title VARCHAR(50) NOT NULL,
	PRIMARY KEY (title_id)
);

CREATE TABLE employees (
	emp_no INT NOT NULL,
	emp_title VARCHAR(5) NOT NULL,
	birth_date VARCHAR(10),
	first_name VARCHAR(30) NOT NULL,
	last_name VARCHAR(30) NOT NULL,
	sex VARCHAR(1),
	hire_date VARCHAR(10) NOT NULL,
	PRIMARY KEY (emp_no),
	FOREIGN KEY (emp_title) REFERENCES titles(title_id)
);

CREATE TABLE departments (
	dept_no VARCHAR(5) NOT NULL,
	dept_name VARCHAR(50) NOT NULL,
	PRIMARY KEY (dept_no)
);

CREATE TABLE salaries (
	emp_no INT NOT NULL,
	salary INT NOT NULL,
	FOREIGN KEY (emp_no) REFERENCES employees(emp_no)
);

CREATE TABLE dept_manager (
	dept_no VARCHAR(5) NOT NULL,
	emp_no INT NOT NULL,
	FOREIGN KEY (dept_no) REFERENCES departments(dept_no),
	FOREIGN KEY (emp_no) REFERENCES employees(emp_no)
);

CREATE TABLE dept_emp (
	emp_no INT NOT NULL,
	dept_no VARCHAR(5) NOT NULL,
	FOREIGN KEY (dept_no) REFERENCES departments(dept_no),
	FOREIGN KEY (emp_no) REFERENCES employees(emp_no)
);
```

[creating_tables.sql](https://github.com/dspataru/sql-challenge/blob/main/EmployeeSQL/creating_tables.sql) contains the code that was written in SQL to create the tables before importing the CSV files into each corressponding table. Item #3 above is also important to consider when importing the data. To avoid import errors, the CSV files were imported into the database in the same order in which the tables were created, while accounting for the headers when doing the imports.

## Data Analysis: Querying the Tables

Eight different queries were run in SQL as part of the data analysis:
1. List the employee number, last name, first name, sex, and salary of each employee.

```sql
SELECT employees.emp_no, employees.last_name, employees.first_name, employees.sex, salaries.salary
FROM employees
INNER JOIN salaries
ON employees.emp_no = salaries.emp_no;
```

2. List the first name, last name, and hire date for the employees who were hired in 1986.

```sql
SELECT first_name, last_name, hire_date
FROM employees
WHERE hire_date LIKE '%/1986';
```

3. List the manager of each department along with their department number, department name, employee number, last name, and first name.

```sql
SELECT dept_manager.dept_no, departments.dept_name, dept_manager.emp_no, employees.last_name, employees.first_name
FROM dept_manager
INNER JOIN departments
ON dept_manager.dept_no = departments.dept_no
INNER JOIN employees
ON dept_manager.emp_no = employees.emp_no;
```

4. List the department number for each employee along with that employee’s employee number, last name, first name, and department name.

```sql
SELECT dept_emp.dept_no, dept_emp.emp_no, employees.last_name, employees.first_name, departments.dept_name
FROM dept_emp
INNER JOIN employees
ON dept_emp.emp_no = employees.emp_no
INNER JOIN departments
ON dept_emp.dept_no = departments.dept_no;
```

5. List first name, last name, and sex of each employee whose first name is Hercules and whose last name begins with the letter B.

```sql
SELECT first_name, last_name, sex
FROM employees
WHERE first_name LIKE '%Hercules%'
  AND last_name LIKE 'B%';
``` 

6. List each employee in the Sales department, including their employee number, last name, and first name.

```sql
SELECT dept_emp.emp_no, employees.first_name, employees.last_name
FROM dept_emp
INNER JOIN employees
ON dept_emp.emp_no = employees.emp_no
INNER JOIN departments
ON dept_emp.dept_no = departments.dept_no
WHERE departments.dept_name = 'Sales';
```

7. List each employee in the Sales and Development departments, including their employee number, last name, first name, and department name.

```sql
SELECT dept_emp.emp_no, employees.first_name, employees.last_name, departments.dept_name
FROM dept_emp
INNER JOIN employees
ON dept_emp.emp_no = employees.emp_no
INNER JOIN departments
ON dept_emp.dept_no = departments.dept_no
WHERE departments.dept_name = 'Sales'
   OR departments.dept_name = 'Development';
```

8. List the frequency counts, in descending order, of all the employee last names (that is, how many employees share each last name).

```sql
SELECT last_name, COUNT(last_name)
FROM employees
GROUP BY last_name
ORDER BY COUNT(last_name) DESC;
```
