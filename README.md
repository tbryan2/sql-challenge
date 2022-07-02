# sql-challenege

------------------------------------------------------------------ CREATE TABLES---------------------



CREATE TABLE "Employees" (
    "emp_no" int   NOT NULL,
    "emp_title_id" VARCHAR   NOT NULL,
    "birth_date" DATE   NOT NULL,
    "first_name" VARCHAR   NOT NULL,
    "last_name" VARCHAR   NOT NULL,
    "sex" CHAR   NOT NULL,
    "hire_date" DATE   NOT NULL,
    CONSTRAINT "pk_Employees" PRIMARY KEY (
        "emp_no"
     )
);

CREATE TABLE "Salaries" (
    "emp_no" INT   NOT NULL,
    "salary" int   NOT NULL
);

CREATE TABLE "Departments" (
    "dept_no" INT   NOT NULL,
    "dept_name" VARCHAR   NOT NULL,
    CONSTRAINT "pk_Departments" PRIMARY KEY (
        "dept_no"
     )
);

CREATE TABLE "dept_emp" (
    "emp_no" int   NOT NULL,
    "dept_no" int   NOT NULL
);

CREATE TABLE "dept_manager" (
    "dept_no" int   NOT NULL,
    "emp_no" int   NOT NULL
);

CREATE TABLE "Titles" (
    "title_id" VARCHAR   NOT NULL,
    "title" VARCHAR   NOT NULL,
    CONSTRAINT "pk_Titles" PRIMARY KEY (
        "title_id"
     )
);

ALTER TABLE "Employees" ADD CONSTRAINT "fk_Employees_emp_title_id" FOREIGN KEY("emp_title_id")
REFERENCES "Titles" ("title_id");

ALTER TABLE "Salaries" ADD CONSTRAINT "fk_Salaries_emp_no" FOREIGN KEY("emp_no")
REFERENCES "Employees" ("emp_no");

ALTER TABLE "dept_emp" ADD CONSTRAINT "fk_dept_emp_emp_no" FOREIGN KEY("emp_no")
REFERENCES "Employees" ("emp_no");

ALTER TABLE "dept_emp" ADD CONSTRAINT "fk_dept_emp_dept_no" FOREIGN KEY("dept_no")
REFERENCES "Departments" ("dept_no");

ALTER TABLE "dept_manager" ADD CONSTRAINT "fk_dept_manager_dept_no" FOREIGN KEY("dept_no")
REFERENCES "Departments" ("dept_no");

ALTER TABLE "dept_manager" ADD CONSTRAINT "fk_dept_manager_emp_no" FOREIGN KEY("emp_no")
REFERENCES "Employees" ("emp_no");



------------------------------------------------------------------ analysis---------------------


-- List the following details of each employee: employee number, last name, first name, sex, and salary.
SELECT "Employees".emp_no, "Employees".last_name, "Employees".first_name, "Employees".sex, "Salaries".salary
FROM "Employees", "Salaries"
WHERE "Employees".emp_no = "Salaries".emp_no;

--List first name, last name, and hire date for employees who were hired in 1986
SELECT first_name, last_name, hire_date
FROM "Employees"
WHERE hire_date BETWEEN '1986-01-01' AND '1986-12-31';

--List the manager of each department with the following information: 
--department number, department name, the manager's employee number, last name, first name.
CREATE VIEW v_department_name AS 
SELECT "dept_manager".dept_no, "Departments".dept_name, "dept_manager".emp_no
FROM "dept_manager", "Departments"
WHERE "dept_manager".dept_no = "Departments".dept_no;

SELECT "v_department_name".dept_no, "v_department_name".dept_name, "v_department_name".emp_no, "Employees".last_name, "Employees".first_name
FROM "v_department_name", "Employees"
WHERE "v_department_name".emp_no = "Employees".emp_no;

--List the department of each employee with the following information: employee number, last name, first name, and department name.
CREATE VIEW dept_num_name AS
SELECT "dept_emp".emp_no, "dept_emp".dept_no, "Departments".dept_name
FROM "dept_emp"
JOIN "Departments" ON "dept_emp".dept_no = "Departments".dept_no

SELECT "Employees".emp_no, "Employees".last_name, "Employees".first_name, "dept_num_name".dept_name
FROM "Employees"
JOIN dept_num_name ON "Employees".emp_no = "dept_num_name".emp_no

--List first name, last name, and sex for employees whose first name is "Hercules" and last names begin with "B."
SELECT first_name, last_name, sex
FROM "Employees"
WHERE first_name = 'Hercules' AND last_name LIKE 'B%'

--List all employees in the Sales department, including their employee number, last name, first name, and department name.
SELECT "Employees".emp_no, "Employees".last_name, "Employees".first_name, "dept_num_name".dept_name
FROM "Employees"
JOIN dept_num_name ON "Employees".emp_no = "dept_num_name".emp_no
WHERE "dept_num_name".dept_name = 'Sales'

--List all employees in the Sales and Development departments, including their employee number, last name, first name, and department name.
SELECT "Employees".emp_no, "Employees".last_name, "Employees".first_name, "dept_num_name".dept_name
FROM "Employees"
JOIN dept_num_name ON "Employees".emp_no = "dept_num_name".emp_no
WHERE "dept_num_name".dept_name IN('Sales', 'Development')

--List the frequency count of employee last names (i.e., how many employees share each last name) in descending order.
SELECT last_name, COUNT(DISTINCT(last_name))
FROM "Employees"
GROUP BY last_name
ORDER BY last_name DESC
