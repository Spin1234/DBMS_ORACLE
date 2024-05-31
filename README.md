# DBMS_ORACLE

## PL/SQL Functions, Procedures and Exception Handling :

### Example:

Function to add two numbers:

```sql
declare
a number;
b number;
c number;
function adder(x in number, y in number)
return number is 
s number;
begin
s:= x+y;
return s;
end;
begin
a :=:a;
b :=:b;
c := adder(a,b);
dbms_output.put_line('Sum of ' || a || ' and ' || b || ' is: '|| c);
end;
```
## Questions:

> Q1. Make a function to calculate total no. Of male and female

> Q2. Make a function to calculate total salary of male and female employees

### Table:

| ID  | NAME      | GENDER | SALARY   |
| :---|:---------:| -----: | :-------:|
| 1   | Amal      | Male   |  23000   |
| 2   | Arunita   | Female |  21000   |
| 3   | Bishal    | Male   |  31000   |
| 4   | Athika    | Female |  43000   |
| 5   | Anita     | Female |  23000   |


Ans1:
```sql
CREATE OR REPLACE FUNCTION total_emp (v_gender IN VARCHAR2) 
RETURN NUMBER 
IS 
total_gen NUMBER(2) := 0; 
BEGIN
SELECT COUNT(*) INTO total_gen FROM EMP1 WHERE GENDER = v_gender;
RETURN total_gen;
END;

set serveroutput on
DECLARE
x NUMBER(2);
BEGIN
x:=total_emp('Male');
DBMS_OUTPUT.PUT_LINE('NO. OF MALE =' || x);
x:=total_emp('Female');
DBMS_OUTPUT.PUT_LINE('NO. OF FEMALE =' || x);
END;
```

Ans2:
```sql
CREATE OR REPLACE FUNCTION total_emp_sal (v_gender IN VARCHAR2) RETURN NUMBER IS total gen_sal NUMBER(2) := 0; BEGIN
SELECT SUM(SALARY) INTO total_gen_sal FROM EMP1 WHERE GENDER = v_gender; RETURN total_gen_sal;
END;

DECLARE
x number(10); 
BEGIN
x:=total_emp_sal('Male'); 
DBMS_OUTPUT.PUT_LINE('TOTAL SAL OF MALE =' || x);
x:=total_emp_sal('Female'); 
DBMS_OUTPUT.PUT_LINE('TOTAL SAL OF FEMALE =' || x);
END;
```

> Q. Create a table that contains passenger details of Railway ticket. Create a procedure to cancel a ticket and keep a copy of that canceled ticket in another table.
    Pass the pnr no. :
      Reservation Table 
      Canceled Table
      
 Ans:
 
 ```sql
 create table train(
pnr number,
name varchar2(100),
seat number
);

insert into train(pnr, name, seat)
values (101, 'arjun', 69); 

insert into train(pnr, name, seat)
values (102, 'arun', 60); 

insert into train(pnr, name, seat)
values (103, 'Bishal', 120); 

create table cancelled(
pnr number,
name varchar2(100),
seat number
);



create or replace procedure cancel_and_save(pnrnum in number)
as
passenger train%rowtype;
begin
select * into passenger from train where pnr = pnrnum;
insert into cancelled(pnr, name, seat)
values(passenger.pnr, passenger.name, passenger.seat);
-- now deleting
delete from train
where pnr = pnrnum;
end;

declare
pnrnum number;
begin
pnrnum:=:pnrnum;
cancel_and_save(pnrnum);
end;

select * from train;

select * from cancelled;
 ```
> Q. Write PL/SQL code block to increment the employee’s salary by 1000 whose emp_id is 102 from the given table below. 

Ans:
```sql
create table employee
(
    emp_id number primary key,
    first_name varchar(255),
    last_name varchar(255),
    doj date,
    salary number
)

insert into employee (emp_id, first_name, last_name, doj, salary) values (101, 'Souvik', 'Ghosh', '17-DEC-2023', 23000)
insert into employee (emp_id, first_name, last_name, doj, salary) values (102, 'Sayan', 'Sit', '13-APR-2023', 23000)
insert into employee (emp_id, first_name, last_name, doj, salary) values (103, 'Amal', 'Sinha', '13-MAY-2022', 33000)
insert into employee (emp_id, first_name, last_name, doj, salary) values (104, 'Asit', 'Ghosh', '10-MAY-2022', 34000)
insert into employee (emp_id, first_name, last_name, doj, salary) values (105, 'Bishal', 'Ghosh', '10-JUNE-2022', 32000)

update employee
set salary = salary + 1000
where emp_id = 102;

Using Cursor:

declare cursor s is
select * from employee where emp_id =:emp_id;
a s%rowtype;
begin
open s;
fetch s into a;
if(s%found) then
   dbms_output.put_line('found');
   update employee 
    set salary = salary + 1000
   where emp_id=a.emp_id;
else
   dbms_output.put_line('not found');
end if;
end;
```

### Exception and Precedure: 

> Q. Create a Procedure to accept an Emp_id, and a salary increase amount, if Emp_id is not found or current salary is NULL then raise exceptions otherwise display total salary

Ans:

```sql
CREATE OR REPLACE PROCEDURE increase_employee_salary (id IN NUMBER, increase_amount IN NUMBER) IS
  current_salary NUMBER;
BEGIN
  SELECT salary INTO current_salary FROM employee WHERE emp_id = id;
  IF current_salary IS NULL THEN
    DBMS_OUTPUT.PUT_LINE('not found');
  ELSE
    current_salary := current_salary + increase_amount;
    UPDATE employee SET salary = current_salary WHERE emp_id = id;
    DBMS_OUTPUT.PUT_LINE('Employee: ' || id || ' new salary:' || current_salary);
  END IF;
EXCEPTION
  WHEN NO_DATA_FOUND THEN
    RAISE_APPLICATION_ERROR(-20002, 'Employee not found.');
END;
BEGIN
  increase_employee_salary(1001, 500);
END;

BEGIN
  increase_employee_salary(101, 500);
END;
```
> Q. Write a Function to find out total annual income for the employee, whose emp_id is 102. 

Ans: 

```sql
CREATE OR REPLACE FUNCTION get_employee_annual_income (id IN NUMBER) RETURN NUMBER
AS
  annual_income NUMBER;
BEGIN
  SELECT (salary * 12)  INTO annual_income FROM employee WHERE emp_id = id;
  RETURN annual_income;
END;

DECLARE
  emp_id NUMBER := 102;
  total_income NUMBER;
BEGIN
  total_income := get_employee_annual_income(emp_id);
  DBMS_OUTPUT.PUT_LINE('Total annual income for employee: ' || emp_id || 'Annual income: ' || total_income);
END;
```

> Q. Write a PL/SQL Block to insert one row in employee table with emp_id and other fields. Display appropriate message using exception handling on duplication entry of emp_id.

Ans: 

```sql
DECLARE
  emp_id NUMBER := 101;
  first_name VARCHAR2(50) := 'Nitin';
  last_name VARCHAR2(50) := 'Saha';
  doj DATE := '12-FEB-2021';
  salary NUMBER := 50000;
BEGIN
 
  INSERT INTO employee (emp_id, first_name, last_name, doj, salary)
  VALUES (emp_id, first_name, last_name, doj, salary);
  
  
  DBMS_OUTPUT.PUT_LINE('Row inserted successfully!');
EXCEPTION
  
  WHEN DUP_VAL_ON_INDEX THEN
    DBMS_OUTPUT.PUT_LINE('Error: Duplicate entry for emp_id ' || emp_id);
END;
```
