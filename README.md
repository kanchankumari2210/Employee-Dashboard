# Employee-Dashboard
The Employee Dashboard by the Department and Avg Salary
/* Task 01*/
/*create visualization that provides a breakdown between the male and female employees working in the company each year starting from 1990*/
select  year(d.from_date) as calender_year,
       e.gender, count(e.emp_no) as num_of_employees
	from 
    t_employees e join 
    t_dept_emp d on d.emp_no = e.emp_no
group by calender_year, e.gender
having calender_year >=1990;
    
/*Task* 02*/
/*Compare the number of male manager to the number of female manager from department for each year, starting 1990*/
select
 d.dept_name,
 ee.gender,
 dm.emp_no,
 dm.from_date,
 e.calender_year,
 case 
  when year(dm.to_date) >= e.calender_year And year(dm.from_date) <= e.calender_year Then 1 
   else 0 
END as active
from 
  (select
   year(hire_date) as calender_year
from 
 t_employees
 group by calender_year) e
 cross join
 t_dept_manager dm
 join 
 t_departments d on dm.dept_no = d.dept_no
 join
 t_employees ee on dm.emp_no = ee.emp_no
 order by dm.emp_no, calender_year;
  
/*Task*/
/*Compare the average salary of female versus male employee in the entire
 company until year 2002, and add fliter allowing you to see the per each department*/
use employees_mod;


 select 
    e.gender, d.dept_name, round(Avg(s.salary),2) as salary, year(s.from_date) as calendar_year
from 
  t_salaries s
   join 
t_employees e on s.emp_no = e.emp_no
  join 
t_dept_emp de on de.emp_no = e.emp_no
  join 
t_departments d on d.dept_no = de.dept_no
group by d.dept_no, e.gender, calendar_year
having calendar_year <= 2002
order by d.dept_no;

/*Create a Store Procedure that allow you to obtain the average male and female salary per department with in certain salary range.*/

drop procedure if exists filter_salary;
Delimiter $$
create procedure filter_salary (In p_min_salary float, In p_max_salary float)
Begin 
Select 
 e.gender, d.dept_name, Avg(s.salary) as avg_salary
from
 t_salaries s
   join
t_employees e on s.emp_no = e.emp_no
  join
t_dept_emp de on  de.emp_no = e.emp_no
 join
 t_departments d on d.dept_no = de.dept_no
 where s.salary between p_min_salary and p_max_salary
 group by d.dept_no, e.gender;
 END$$
 Delimiter ;
 
 call filter_salary(50000, 90000);
