1.
create or replace procedure add_job
( p_id jobs.job_id%type,
p_title jobs.job_title%type )
is 
begin
insert into jobs ( job_id, job_title)
values ( p_id, p_title);
end add_job;

execute add_job('ADM_ASS','Chief Operating Officer')

select * from jobs;

2.
create or replace procedure upd_job
( p_id IN jobs.job_id%type,
p_title IN jobs.job_title%type )
is 
begin
update jobs
set job_title = p_title
where job_id = p_id;
if sql%notfound then
raise_application_error (-20202, 'No data updated.');
end if;
end upd_job;

execute upd_job('ADM_ASS','Chief Executive Officer')

select * from jobs where job_id = 'ADM_ASS';

3.
create or replace procedure del_job
( p_id jobs.job_id%type)
is 
begin
delete from jobs
where job_id = p_id;
end del_job;

execute del_job('ADM_ASS')

4.
create or replace procedure get_employee
( p_id IN employees.employee_id%type,
p_sal out employees.salary%type,
p_jobid out employees.job_id%type )
is
begin
select salary, job_id into p_sal , p_jobid 
from employees
where employee_id = p_id ;
end get_employee ;

variable v_sal NUMBER
variable v_jobid varchar2(20)
execute get_employee(101, :v_sal, :v_jobid)

print v_sal v_jobid

5.
create or replace procedure raise_salary
( p_id IN employees.employee_id%type,
p_percent IN number )
is
begin
update employees
set salary = salary * (1+p_percent/100)
where employee_id = p_id;
end raise_salary;

execute raise_salary (101,10)

select salary from employees where employee_id = 101;

6.
create or replace procedure query_emp
( p_id IN employees.employee_id%type,
p_name OUT employees.last_name%type,
p_sal OUT employees.salary%type )
is begin
select last_name, salary into p_name, p_sal
from employees
where employee_id = p_id;
end query_emp;

declare
v_name employees.last_name%type;
v_sal employees.salary%type;
begin
query_emp (101, v_name , v_sal );
dbms_output.put_line ( v_name || ' earns ' || v_sal );
end query_emp;

7.
create or replace procedure add_dept
( p_name departments.department_name%type,
p_loc departments.location_id%type )
is
begin
insert into departments (department_id, department_name, location_id )
values (departments_seq.nextval, p_name, p_loc );
end add_dept ;

8.
create or replace function get_sal
( p_id employees.employee_id%type)
return number
is
v_sal employees.salary%type;
begin
select salary into v_sal
from employees
where employee_id = p_id;
return v_sal;
end get_sal;

execute dbms_output.put_line (get_sal(100))

9.
create or replace function tax
( p_value IN number)
return number
is
begin
return (p_value * 0.08);
end tax;

select employee_id , last_name, salary, tax(salary)
from employees
where employee_id = 100;

10.
create or replace function get_job
( p_jobid IN jobs.job_id%type )
return jobs.job_title%type
is
v_title jobs.job_title%type;
begin
select job_title into v_title
from jobs
where job_id = p_jobid;
return v_title;
end get_job;

variable b_title varchar2(35)
execute :b_title := get_job('SA_REP');

print b_title

11.
create or replace function get_annual_comp
( p_sal IN employees.salary%type,
p_com IN employees.commission_pct%type )
return number 
is
begin
return (nvl(p_sal,0)*12 +(nvl(p_com,0)*nvl(p_sal,0)*12));
end get_annual_comp;


select employee_id, last_name, get_annual_comp(salary,commission_pct) "Annual Compensation"
from employees
where department_id = 30


12.
create or replace function valid_deptid
( p_deptid IN departments.department_id%type )
return boolean
is
v_dummy pls_integer;
begin
select 1 into v_dummy
from departments
where department_id = p_deptid ;
return true;
exception
when no_data_found then
return false;
end valid_deptid;


create or replace procedure add_employee
( p_first_name employees.first_name%type,
p_last_name employees.last_name%type,
p_email employees.email%type,
p_job employees.job_id%type default 'SA_REP',
p_mgr employees.manager_id%type default 145,
p_sal employees.salary%type default 1000,
p_comm employees.commission_pct%type default 0,
p_deptid employees.department_id%type default 30)
is
begin
if valid_deptid(p_deptid) then
insert into employees ( employee_id, first_name, last_name, email, job_id, manager_id, salary, commission_pct, department_id )
values (employees_seq.nextval, p_first_name , p_last_name , p_email , p_job , p_mgr , p_sal , p_comm , p_deptid );
else
raise_application_error ( -20204, 'Invalid department ID. Try again.');
end if;
end add_employee;

execute add_employee('Kabir', 'Hossan', 'hossankabirq', p_id  => 80)

13.
Create or replace procedure process_employees
is
cursor cur_emp_cursor is
select employee_id
from employees;
begin
for emp_rec IN cur_emp_cursor
loop
raise_salary (emp_rec.employee_id, 10);
end loop;
end process_employees;








