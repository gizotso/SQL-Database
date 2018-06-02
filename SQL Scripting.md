# SQL Scripting

## Substitution Variables
substitution variables can replace SQL*Plus command options or other hard-coded text
SQL Developer supports substitution variables, but when you execute a query with bind :var syntax you are prompted for the binding (in a dialog box).
wihtout `DEFINE arg` &arg will result in SQL*Plus or SQLDeveloper prompting for value : "SQL: Enter value for arg:" (Enter Substitution variable popup in SQL developer)
`SET DEFINE OFF` disables the parsing of commands to replace substitution variables with their values.

```
DEFINE arg=first_name;
SELECT id, login_name, &arg. FROM users WHERE id < 10;
UNDEFINE arg;

DEFINE p_id=10;
SELECT id, login_name FROM users WHERE id < &p_id;

# owner has not be defined, then value will be prompt
# with &&, prompt is asked only once (permanent (&&) vs temporary (&) substitution variable)
select count(*) from user_tables where owner='&&owner';

# define a subst variable and query from dual do print it
DEFINE db_user=bi4;
SELECT '&db_user' db_user FROM dual;

DEFINE db_user="user/pwd@Database";
SELECT '&db_user' db_user FROM dual;

# define a subst variable and display it on a PL/SQL block
set serveroutput on;
DEFINE db_user=bi4;
BEGIN
   DBMS_OUTPUT.PUT_LINE('db_user: &db_user');
END;

# initialize a PL/SQL variable with using a substitution variable
DECLARE
   l_db_link_n varchar2(100) := '&db_link_name';
BEGIN
   ...
END;

DECLARE
   l_db_link_n varchar2(100);
BEGIN   
   SELECT '&db_link_name' INTO l_db_link_n FROM dual;
END;

# use a substitution variable in an include command (@)
define sqlscript=E:\dwh\bi\sql
@&sqlscripts\users.sql
```

### Predefined Substitution variables
`DEFINE` will print all substitution variables including predefined
```
_DATE
_USER
_CONNECT_IDENTIFIER
```

### Storing a Query Column Value in a Substitution Variable
```
COLUMN login_name new_value v_login
SELECT login_name FROM user sWHERE id= 9;
SELECT '&v_login' FROM dual;

exec DBMS_OUTPUT.PUT_LINE('v_login: &v_login');
```

* https://blogs.oracle.com/opal/sqlplus-101-substitution-variables
* [OraFAQ : Spice up your SQL scripts with variables](http://www.orafaq.com/node/515) : &, &&, &n, &var1., UNDEF
* [DEFINE on www.adp-gmbh.ch/ora] (http://www.adp-gmbh.ch/ora/sqlplus/define.html)
* [dba-oracle : oracle tips columns variables](http://www.dba-oracle.com/oracle_tips_columns_variables.htm)

## Bind Variables
Bind variables store data values for SQL and PL/SQL statements executed in the RDBMS. 
They can hold single values or complete result sets


Using Run Statement in SQL Developer will prompt for the bind value ("Enter Binds") if it has been defined or not.
Using Run Script in SQL Developer of running in SQL*Plus without having declared the bind will result in an Error : "SP2-0552: Bind variable PID not declared."
```
SELECT * FROM usersWHERE id = :pid;
```

```
# create a bind variable x
variable x number;
# set x to 10
exec :x := 10;
# print x value
print x;

# set x to 30 and print value within a PL/SQL block
var x number;
BEGIN   
   :x := 30;   
   DBMS_OUTPUT.PUT_LINE('x=' || :x);
END;
/


var p_in number;
exec :p_in := 8;
SELECT * FROM users WHERE id = :p_in;

var x number;
exec SELECT count(1) INTO :x FROM user_tables;
print x;
```

   - https://docs.oracle.com/cd/A81042_01/DOC/sqlplus.816/a75664/ch34.htm
   - http://www.adp-gmbh.ch/ora/sqlplus/use_vars.html
   - https://docs.oracle.com/cd/A81042_01/DOC/sqlplus.816/a75664/ch34.htm
   - http://gerardnico.com/wiki/sqlplus/bind_variable
   - [SQL*Plus tips : initialisation env](http://orasql.org/2013/03/29/sqlplus-tips-1/)

## SQL script shell launcher
- https://oracle-base.com/articles/misc/oracle-shell-scripting
- https://danielarancibia.wordpress.com/2014/06/29/execute-scripts-with-sqlplus-on-a-windows-batch-file/

### Passing parameters to SQL scripts from shell
`sqlplus $db_conn @add_user.sql TOTO`

- `&1.` : SQL substitution variable which contain first passed argument
- `&2.`
