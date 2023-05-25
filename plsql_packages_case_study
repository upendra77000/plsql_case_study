SET SERVEROUTPUT ON;

CREATE TABLE xy_employees(
        emp_id NUMBER, 
        emp_name VARCHAR2(30), 
        doj DATE, 
        dob DATE, 
        emp_location VARCHAR2(30),
        job_role VARCHAR2(30),
        manager_id NUMBER, 
        salary NUMBER, 
        email VARCHAR2(30), 
        created_by NUMBER,
        creation_date DATE,
        last_updated_by NUMBER,
        last_update_date DATE,
        last_update_login NUMBER,
        status VARCHAR2(30)
        );

CREATE TABLE xx_employees(
        emp_id NUMBER, 
        emp_name VARCHAR2(30), 
        doj DATE, 
        dob DATE, 
        emp_location VARCHAR2(30), 
        manager_id NUMBER, 
        salary NUMBER, 
        email VARCHAR2(30), 
        created_by NUMBER,
        creation_date DATE,
        last_updated_by NUMBER,
        last_update_date DATE,
        last_update_login NUMBER,
        status VARCHAR2(30)
        );

-- PACKAGE SPECIFICATION
create or replace PACKAGE up_emp_package
IS
    TYPE emp_record_type IS RECORD (
        g_emp_id NUMBER, 
        g_emp_name VARCHAR2(30), 
        g_doj DATE, 
        g_dob DATE, 
        g_emp_location VARCHAR2(30), 
        g_manager_id NUMBER, 
        g_salary NUMBER, 
        g_email VARCHAR2(30), 
        g_created_by NUMBER,
        g_creation_date DATE,
        g_last_updated_by NUMBER,
        g_last_update_date DATE,
        g_last_update_login NUMBER,
        g_status VARCHAR2(30));     

    PROCEDURE create_emp(p_emp_record emp_record_type, p_status OUT VARCHAR2, p_msg OUT VARCHAR2);
    PROCEDURE create_emp(p_emp_record emp_record_type, p_job_role VARCHAR2, p_status OUT VARCHAR2, p_msg OUT VARCHAR2);
    FUNCTION get_emp_exp(f_doj DATE, f_experience OUT NUMBER) RETURN NUMBER;
    FUNCTION fire_emp(f_emp_id NUMBER, f_msg OUT VARCHAR2) RETURN VARCHAR2;
    FUNCTION salary_hike(f_emp_id NUMBER, f_doj DATE, f_msg OUT VARCHAR2) RETURN VARCHAR2; 
END up_emp_package;
/

drop package emp_package;

-- PACKAGE BODY
create or replace PACKAGE BODY emp_package
IS
    -- PROCEDURE FOR CREATING AN EMPLOYEE
    PROCEDURE create_emp(p_emp_record emp_record_type, p_status OUT VARCHAR2, p_msg OUT VARCHAR2)
    IS
        BEGIN
            p_status := 'Failed';
            INSERT INTO xx_employees VALUES p_emp_record;
            l_cnt := SQL%ROWCOUNT;
            COMMIT;
            p_msg := 'Insert Successfull';
            p_status := 'Success';
        EXCEPTION
            WHEN OTHERS THEN
                dbms_output.put_line(DBMS_UTILITY.format_error_stack || DBMS_UTILITY.format_error_backtrace);
                dbms_output.put_line('Insert Unsuccessfull');
        END create_emp;
    
    -- PROCEDURE FOR CREATING AN EMPLOYEE
    PROCEDURE create_emp(p_emp_record emp_record_type, p_job_role VARCHAR2, p_status OUT VARCHAR2, p_msg OUT VARCHAR2)
    IS 
        BEGIN
            p_status := 'Failed';
            INSERT INTO xy_employees (
                            emp_id, 
                            emp_name, 
                            doj, 
                            dob, 
                            emp_location,
                            job_role,
                            manager_id, 
                            salary, 
                            email,
                            created_by,
                            creation_date,
                            last_updated_by,
                            last_update_date,
                            last_update_login,
                            status) 
                        VALUES (
                            p_emp_record.g_emp_id,
                            p_emp_record.g_emp_name, 
                            p_emp_record.g_doj,
                            p_emp_record.g_dob, 
                            p_emp_record.g_emp_location, 
                            p_job_role,
                            p_emp_record.g_manager_id, 
                            p_emp_record.g_salary, 
                            p_emp_record.g_email, 
                            p_emp_record.g_created_by, 
                            p_emp_record.g_creation_date, 
                            p_emp_record.g_last_updated_by,
                            p_emp_record.g_last_update_date, 
                            p_emp_record.g_last_update_login, 
                            p_emp_record.g_status);    
             p_msg := 'Insert Successfull';
             p_status := 'Success';
        EXCEPTION
            WHEN OTHERS THEN
                dbms_output.put_line(DBMS_UTILITY.format_error_stack || DBMS_UTILITY.format_error_backtrace);
                dbms_output.put_line('Insert Unsuccessfull');
        END create_emp;

    -- FUNCTION FOR FINDING EMPLOYEE EXPERIENCE
    FUNCTION get_emp_exp(f_doj DATE, f_experience OUT NUMBER)
    RETURN NUMBER
    IS 
        BEGIN
            f_experience := ROUND((SYSDATE - f_doj)/365, 2); 
        RETURN f_experience;
        EXCEPTION
            WHEN OTHERS THEN
                dbms_output.put_line(DBMS_UTILITY.format_error_stack || DBMS_UTILITY.format_error_backtrace); 
        END get_emp_exp;

    -- FUNCTION FOR FIRING AN EMPLOYEE
    FUNCTION fire_emp(f_emp_id NUMBER, f_msg OUT VARCHAR2) 
    RETURN VARCHAR2
    IS 
        BEGIN
            UPDATE xy_employees SET status = 'Inactive' WHERE emp_id = f_emp_id;
            f_msg := 'Employee ' || f_emp_id || ' removed';
            RETURN f_msg;
        EXCEPTION
            WHEN OTHERS THEN
                dbms_output.put_line(DBMS_UTILITY.format_error_stack || DBMS_UTILITY.format_error_backtrace); 
        END fire_emp;

    -- FUNCTION FOR HIKING THE SALARY
    FUNCTION salary_hike(f_emp_id NUMBER, f_doj DATE, f_msg OUT VARCHAR2)
    RETURN VARCHAR2
    IS 
        l_exp NUMBER; 
        l_sal NUMBER; 
        l_hiked_sal NUMBER;
        BEGIN
            SELECT salary INTO l_sal FROM xy_employees WHERE emp_id = f_emp_id; 
            l_exp := get_emp_exp(f_doj, l_exp); 
            IF l_exp<5 THEN
                l_hiked_sal := l_sal + (l_sal*10)/100;
            ELSIF l_exp>=5 AND l_exp<10 THEN
                l_hiked_sal := l_sal + (l_sal*15)/100;
            ELSIF l_exp>=10 AND l_exp<20 THEN
                l_hiked_sal := l_sal + (l_sal*20)/100;
            ELSE
                l_hiked_sal := l_sal + (l_sal*5)/100;
            END IF;

            UPDATE xy_employees SET salary = l_hiked_sal WHERE emp_id = f_emp_id; 

            f_msg := 'Employee ' || f_emp_id || ' Salary' || ' has been hiked from ' || l_sal || ' to ' || l_hiked_sal;
            RETURN f_msg; 
        EXCEPTION
            WHEN OTHERS THEN
                dbms_output.put_line(DBMS_UTILITY.format_error_stack || DBMS_UTILITY.format_error_backtrace);      
        END salary_hike; 
END emp_package;
/

-- CREATING AN EMPLOYEE
DECLARE
    l_emp emp_package.emp_record_type;
    l_job_role VARCHAR2(30);
    l_status VARCHAR2(30);
    l_msg VARCHAR2(50);
BEGIN
    l_emp.g_emp_id := 1;    
    l_emp.g_emp_name := 'Ajay';  
    l_emp.g_doj := '23-FEB-2003';
    l_emp.g_dob := '09-DEC-1972'; 
    l_emp.g_emp_location := 'HYD'; 
    l_job_role := 'Software Engineer';
    l_emp.g_manager_id := 10; 
    l_emp.g_salary := 650000;
    l_emp.g_email := 'ajay@ltimindtree.com';
    l_emp.g_created_by := 109;
    l_emp.g_creation_date := SYSDATE;
    l_emp.g_last_updated_by := 109;
    l_emp.g_last_update_date := SYSDATE;
    l_emp.g_last_update_login := 109;
    l_emp.g_status := 'Active';
    emp_package.create_emp(l_emp, l_job_role, l_status, l_msg);
    dbms_output.put_line(l_msg);
END;
/

-- FINDING EMPLOYEE EXPERIENCE
DECLARE
    l_exp NUMBER; 
    l_emp_id NUMBER;
    l_doj DATE;
BEGIN
    SELECT emp_id, doj INTO l_emp_id, l_doj FROM xy_employees WHERE emp_id = 1;
    l_exp := emp_package.get_emp_exp(l_doj, l_exp);
    dbms_output.put_line('Employee ' || l_emp_id || ' Experience is: ' || l_exp);
END;
/

-- FIRING AN EMPLOYEE
DECLARE
    l_emp_id NUMBER := 1;
    l_msg VARCHAR2(30);
BEGIN
    l_msg := emp_package.fire_emp(l_emp_id, l_msg);
    dbms_output.put_line(l_msg);
END;
/

-- SALARY HIKING 
DECLARE
    l_emp_id NUMBER;
    l_doj DATE;
    l_msg VARCHAR2(60);
BEGIN
    SELECT emp_id, doj INTO l_emp_id, l_doj FROM xy_employees WHERE emp_id = 1;
    l_msg := emp_package.salary_hike(l_emp_id, l_doj, l_msg);
    dbms_output.put_line(l_msg);
END;
/

CREATE SEQUENCE log_sequence
START WITH 1
INCREMENT BY 1;

CREATE TABLE log_table(
                log_id NUMBER PRIMARY KEY,
                log_timestamp TIMESTAMP,
                event_type VARCHAR2(255),
                log_user NUMBER,
                table_name VARCHAR2(255),
                old_data VARCHAR2(255),
                new_data VARCHAR2(255),
                log_description VARCHAR2(255)
                ); 
/

CREATE OR REPLACE TRIGGER update_trigger
AFTER UPDATE ON xy_employees 
FOR EACH ROW
BEGIN
    INSERT INTO log_table VALUES (
                            log_sequence.nextval,
                            CURRENT_TIMESTAMP,
                            'Update',
                            109,
                            'xy_employees',
                            'salary:' || :OLD.salary,
                            'salary:'|| :NEW.salary, 
                            'A row has been updated'
                            );
END;
/

CREATE OR REPLACE TRIGGER insert_trigger
AFTER INSERT ON xy_employees 
FOR EACH ROW
BEGIN
    INSERT INTO log_table VALUES (
                            log_sequence.nextval,
                            CURRENT_TIMESTAMP,
                            'Insert',
                            109,
                            'xy_employees',
                            'salary:' || :OLD.salary,
                            'salary:'|| :NEW.salary, 
                            'A row has been Inserted'
                            );
END;
/
