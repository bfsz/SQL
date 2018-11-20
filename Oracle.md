# Oracle

[TOC]

## 一、查询

### 1、SELECT

列选择

```sql
SELECT Col1,Col2 FROM table1;
```

行选择

```sql
SELECT * FROM table1;
```

多表连接查询

```sql
SELECT T1.col1,T2.col2 FROM Table1 T1,Table2 T2;
```

别名

```sql
SELECT t.last_name (AS) name FROM Table t;
```
字符串连接操作符：“||”

```sql
SELECT t1,last_name || 'is a' || t1.job AS 'Details' FROM Table1 t1;
```
去除重复行：“DISTINCT”

```sql
SELECT DISTINCT t1.sal FROM table1 t1;
```
### 2、条件限制和排序

- WHERE

```sql
SELECT * FROM table1;
```
- 比较操作符

| 比较操作符       | 意义                          |
| ---------------- | ----------------------------- |
| =                | 等于                          |
| >                | 大于                          |
| <                | 小于                          |
| >=               | 大于等于                      |
| <=               | 小于等于                      |
| <>               | 不等于                        |
| BETWEEN...AND... | 在两个值之间                  |
| IN(set)          | 在一个集合范围内              |
| LIKE             | 匹配一个字符串，可使用%通配符 |
| IS NULL          | 是空值，不能使用=NULL         |

```sql
--查询工资大于1000且部门为30的所有信息
SELECT * FROM emp WHERE sal>1000 and deptno = 30;
--查询工资sal在1000-2000的所有信息
select * from emp p where p.sal between 1000 and 2000;
--查询部门为30、40的所有信息
select * from emp where deptno in(30,40);
--查询名字以N结尾的员工信息
select * from emp where ename like '%N';
--查询名字中含有N的员工信息
select * from emp where ename like '%N%';
--查询名字中第三个字母为M的员工信息
select * from emp where ename like '__M%';
--查询含有%的记录
select * from emp where ename like '%\%%' escape '\';
--查询NULL
select * from emp where comm is null;
```

- 逻辑操作符

| 逻辑操作符 | 意义                       |
| ---------- | -------------------------- |
| AND        | 所有条件都满足，返回true   |
| OR         | 只要一个条件满足，返回true |
| NOT        | 如果条件是false，返回true  |

```sql
select * from emp p where p.job = 'CLERK' and p.deptno = '40';
select * from emp p where p.job not in('CLERK','MANAGER'); 
select * from emp p where p.job = 'CLERK' or p.deptno = '40';
```

- ORDER BY子句排序

  ASC: 升序

  DESC:降序

```sql
select * from emp order by empno;
--升序
select * from emp order by empno asc;
--降序
select * from emp order by empno desc;
--别名排序
select empno,ename,(sal*10) sals from emp p order by sals asc;
--多字段排序
select empno, ename, (sal * 10) sals from emp p order by sals, empno asc;

```

### 3、函数

#### 1、大小写转换字符

​	oracle数据库中的数据是大小写敏感的。

| 函数                | 结果     |
| ------------------- | -------- |
| LOWER(’SQL plus‘)   | sql plus |
| UPPER('SQL plus')   | SQL PLUS |
| INITCAP('SQL plus') | Sql Plus |

```sql
select empno, lower(ename) from emp ;
```

#### 2、字符操作函数

| 函数                     | 结果       | 意义               |
| :----------------------- | ---------- | ------------------ |
| CONCAT('a','b')          | ab         | 拼接               |
| SUBSTR('HelloWorld',1,5) | Hello      | 截取               |
| LENGTH('Hello')          | 5          | 字符长度           |
| INSTR('Hello','l')       | 3          | 获取字符位置       |
| LPAD(sal,10,'A')         | AAAAAAA800 |                    |
| RPAD(sal,10,'A')         | 800AAAAAAA |                    |
| TRIM(' Helloworld ')     | Helloworld | 去除字符串前后空格 |
| TRIM('H'FROM'Hello')     | ello       |                    |
| LTRIM/RTRIM              |            | 去除左/右空格      |

#### 3、数字操作函数s

| 函数            | 结果      |
| --------------- | --------- |
| ROUND(45.926,2) | 45.93     |
| TRUNC(45.926,2) | 45.92     |
| MOD(1600,300)   | 100(求余) |

- TO_CHAR()数字到字符串的转换

```sql
TO_CHAR(number,'format_model');
--
select TO_CHAR(sal) sal from emp;
```

- TO_NUMBER()字符串到数字的转换

```sql
select to_number('4456') num from dual;
select to_number('$4,456','$9999') num from dual;
select to_number('$4,456,455,000','$9,999,999,999') num from dual;
```

#### 4、日期操作函数⏲

​	to_date("要转换的字符串","转换的格式")   两个参数的格式必须匹配，否则会报错。

即按照第二个参数的格式解释第一个参数。

​	to_char(日期,"转换格式" ) 即把给定的日期按照“转换格式”转换。

```SQL
select to_date('22-FEB-11') from dual;
select to_date('2018-09-11','YYYY-MM-DD') from dual;
select to_date('02/22/2018','MM/DD/YYYY') from dual;
```

##### 1 **转换的格式**

**year**：y 表示年的最后一位 yy 表示年的最后2位 yyy 表示年的最后3位 yyyy 用4位数表示年

**month**：mm 用2位数字表示月；mon 用简写形式 比如11月或者nov ；month 用全称 比如11月或者november

**day**：dd 表示当月第几天；ddd表示当年第几天；dy 当周第几天 简写 比如星期五或者fri；day当周第几天 全写

比如星期五或者friday。

**hour**：hh 2位数表示小时 12进制； hh24 2位数表示小时 24小时

**minute**：mi 2位数表示分钟

**second**：ss 2位数表示秒 60进制

表示季度的：q 一位数 表示季度 （1-4）

**ww** 用来表示当年第几周 w用来表示当月第几周。

24小时制下的时间范围：00：00：00-23：59：59

12小时制下的时间范围：1：00：00-12：59：59

比如：

```sql
select to_char(sysdate,'yy-mm-dd hh24:mi:ss') from dual   
--显示：08-11-07 13:22:42
select to_date('2005-12-25,13:25:59','yyyy-mm-dd,hh24:mi:ss') from dual 
--显示：2005-12-25 13:25:59
```

而如果把上式写作：select to_date('2005-12-25,13:25:59','yyyy-mm-dd,hh:mi:ss') from dual，则会报错，因为小时hh是12进制，13为非法输入，不能匹配。

**补充**

```sql
--当前时间减去7分钟的时间 
select sysdate,sysdate - interval '7' MINUTE from dual 
--当前时间减去7小时的时间 
select sysdate - interval '7' hour from dual 
--当前时间减去7天的时间 
select sysdate - interval '7' day from dual 
--当前时间减去7月的时间 
select sysdate,sysdate - interval '7' month from dual 
--当前时间减去7年的时间 
select sysdate,sysdate - interval '7' year from dual 
--时间间隔乘以一个数字 
select sysdate,sysdate - 8*interval '7' hour from dual
```

**Dual伪列**

含义解释：

Dual 是 Oracle中的一个实际存在的表，任何用户均可读取，常用在没有目标表的select语句块中。

比如，我要获得系统时间，则用“select sysdate from dual” 则返回系统当前的时间：2008-11-07 9:32:49，不同系统可能返回日期的格式不一样。"select user from dual"则返回当前连接的用户。如果是"select 1+2 from dual"，则返回结果：3


**TO_DATE格式(以时间:2007-11-02   13:45:25为例)**

```sql
    Year:      
    yy two digits 两位年                显示值:07
    yyy three digits 三位年                显示值:007
    yyyy four digits 四位年                显示值:2007
        
    Month:      
    mm    number     两位月              显示值:11
    mon    abbreviated 字符集表示          显示值:11月,若是英文版,显示nov     
    month spelled out 字符集表示          显示值:11月,若是英文版,显示november 
      
    Day:      
    dd    number         当月第几天        显示值:02
    ddd    number         当年第几天        显示值:02
    dy    abbreviated 当周第几天简写    显示值:星期五,若是英文版,显示fri
    day    spelled out   当周第几天全写    显示值:星期五,若是英文版,显示friday        
    ddspth spelled out, ordinal twelfth 
         
          Hour:
          hh    two digits 12小时进制            显示值:01
          hh24 two digits 24小时进制            显示值:13
          
          Minute:
          mi    two digits 60进制                显示值:45
          
          Second:
          ss    two digits 60进制                显示值:25
          
          其它
          Q     digit          季度                  显示值:4
          WW    digit          当年第几周            显示值:44
          W     digit          当月第几周            显示值:1
          
    24小时格式下时间范围为： 0:00:00 - 23:59:59 ....      
    12小时格式下时间范围为： 1:00:00 - 12:59:59 .... 
```

##### 2 **日期和字符转换函数用法（to_date,to_char）**

```sql
--日期转化为字符串   
select to_char(sysdate,'yyyy-mm-dd hh24:mi:ss') as nowTime from dual;   
--获取时间的年
select to_char(sysdate,'yyyy') as nowYear   from dual;      
--获取时间的月  
select to_char(sysdate,'mm')    as nowMonth  from dual; 
--获取时间的日
select to_char(sysdate,'dd')    as nowDay    from dual;
--获取时间的时 
select to_char(sysdate,'hh24')  as nowHour    from dual;
--获取时间的分 
select to_char(sysdate,'mi')    as nowMinute from dual;
--获取时间的秒
select to_char(sysdate,'ss')    as nowSecond from dual;   
select to_date('2004-05-07 13:23:44','yyyy-mm-dd hh24:mi:ss') from dual;
```

2. 
    select to_char( to_date(222,'J'),'Jsp') from dual      

    显示Two Hundred Twenty-Two    

##### 3 求某天是星期几      

```sql
select to_char(to_date('2018-07-30','yyyy-mm-dd'),'day') from dual;          
```

##### 4 设置日期语言      

```sql
select to_char(to_date('2002-08-26','yyyy-mm-dd'),'day','NLS_DATE_LANGUAGE = American') from dual;  
```

```sql
--
TO_DATE ('2002-08-26', 'YYYY-mm-dd', 'NLS_DATE_LANGUAGE = American')
--
ALTER SESSION SET NLS_DATE_LANGUAGE='AMERICAN';      
```

##### 5 两个日期间的天数      

```sql
select floor(sysdate - to_date('19940911','yyyymmdd')) from dual;
```

##### 6 时间为null的用法      

```sql
select id, active_date from table1 UNION select 1, TO_DATE(null) from dual;  
```

注意要用TO_DATE(null)    

##### 7 日期格式冲突问题      

输入的格式要看你安装的ORACLE字符集的类型, 比如: US7ASCII, date格式的类型就是: '01-Jan-01'      
alter system set NLS_DATE_LANGUAGE = American      
alter session set NLS_DATE_LANGUAGE = American      
或者在to_date中写      
select to_char(to_date('2002-08-26','yyyy-mm-dd'),'day','NLS_DATE_LANGUAGE = American') from dual;      
注意我这只是举了NLS_DATE_LANGUAGE，当然还有很多，      
可查看      
select * from nls_session_parameters      
select * from V$NLS_PARAMETERS    

##### 8 查找t1至t2间除星期一和七的天数

```sql
select count(*)      
from ( select rownum-1 rnum      
   	   from all_objects      
       where rownum <= to_date('2002-02-28','yyyy-mm-dd') - to_date('2002-02-01','yyyy-mm-dd')+1 
   )      
where to_char( to_date('2002-02-01','yyyy-mm-dd')+rnum-1, 'D' ) not in ( '1', '7' )  
```

在前后分别调用DBMS_UTILITY.GET_TIME, 让后将结果相减(得到的是1/100秒, 而不是毫秒).    

##### 9 查找月份     

```sql
select months_between(to_date('01-31-1999','MM-DD-YYYY'),to_date('12-31-1998','MM-DD-YYYY')) "MONTHS" FROM DUAL;  
--1      
```

```sql
 select months_between(to_date('02-01-1999','MM-DD-YYYY'),to_date('12-31-1998','MM-DD-YYYY')) "MONTHS" FROM DUAL;  
 --1.03225806451613     
```

##### 10 获得小时数      

​     extract()找出日期或间隔值的字段值

```sql
 SELECT EXTRACT(HOUR FROM TIMESTAMP '2001-02-16 2:38:40') from dual  
```

​    SQL> select sysdate ,to_char(sysdate,'hh') from dual;      
​    

```sql
SYSDATE TO_CHAR(SYSDATE,'HH')      
-------------------- ---------------------      
2003-10-13 19:35:21 07      

SQL> select sysdate ,to_char(sysdate,'hh24') from dual;      

SYSDATE TO_CHAR(SYSDATE,'HH24')      
-------------------- -----------------------      
2003-10-13 19:35:21 19    
```

##### 11 年月日的处理      

```sql
 select older_date,
        newer_date,
        years,
        months,
        abs(trunc(newer_date - add_months(older_date, years * 12 + months))) days
   from (select trunc(months_between(newer_date, older_date) / 12) YEARS,
                mod(trunc(months_between(newer_date, older_date)), 12) MONTHS,
                newer_date,
                older_date
           from (select hiredate older_date,
                        add_months(hiredate, rownum) + rownum newer_date
                   from emp))
```

##### 12 处理月份天数不定的办法      

```sql
select to_char(add_months(last_day(sysdate) +1, -2), 'yyyymmdd'),last_day(sysdate) from dual 
```

##### 13 找出今年的天数            

```sql
select add_months(trunc(sysdate,'year'), 12) - trunc(sysdate,'year') from dual     
```

##### 14 不同时区的处理           

```sql
select to_char(NEW_TIME(sysdate, 'GMT', 'EST'), 'dd/mm/yyyy hh:mi:ss'),sysdate from dual;
```

##### 15 5秒钟一个间隔         

```sql
Select TO_DATE(FLOOR(TO_CHAR(sysdate,'SSSSS')/300) * 300,'SSSSS') ,TO_CHAR(sysdate,'SSSSS')   from dual 
```

   2002-11-1 9:55:00 35786      
   SSSSS表示5位秒数    

##### 16 一年的第几天              

```sql
select TO_CHAR(SYSDATE, 'DDD'), sysdate from dual 
--212   2018/7/31 9:04:23
```

##### 17 计算小时,分,秒,毫秒      

   

##### 18 next_day函数      

返回下个星期的日期,day为1-7或星期日-星期六,1表示星期日,next_day(sysdate,6)是从当前开始下一个星期五。后面的数字是从星期日开始算起。      

```sql
--1 2 3 4 5 6 7      
--日 一 二 三 四 五 六    
select next_day(sysdate,1) day from dual;
--2018/8/5 9:09:18

--日期 返回的是天 然后 转换为ss
select (sysdate - to_date('2018-07-31 09:10:45', 'yyyy-mm-dd hh24:mi:ss'))*2460 * 60  from dual
```

##### 19 round舍入到最接近的日期

```sql
select sysdate S1,
       round(sysdate) S2,
       round(sysdate, 'year') YEAR,
       round(sysdate, 'month') MONTH,
       round(sysdate, 'day') DAY
  from dual
```

##### 20 trunc[截断到最接近的日期,单位为天] 

​	返回的是日期类型   

```sql
select sysdate S1,                     
 trunc(sysdate) S2,                --返回当前日期,无时分秒
 trunc(sysdate,'year') YEAR,        --返回当前年的1月1日,无时分秒
 trunc(sysdate,'month') MONTH ,     --返回当前月的1日,无时分秒
 trunc(sysdate,'day') DAY           --返回当前星期的星期天,无时分秒
from dual
```

##### 21 返回日期列表中最晚日期  

```sql
select greatest('2014-07-24','2015-09-04','2018-11-04') from dual
```

##### 22 计算时间差

​     注:oracle时间差是以天数为单位,所以换算成年月,日  

```sql
select floor(to_number(sysdate-to_date('1994-09-11 15:55:03','yyyy-mm-dd hh24:mi:ss'))/365) as spanYears from dual        --时间差-年
select ceil(MONTHS_BETWEEN(sysdate,to_date('2007-11-02 15:55:03','yyyy-mm-dd hh24:mi:ss'))) as spanMonths from dual        --时间差-月
select floor(to_number(sysdate-to_date('1994-09-11 15:55:03','yyyy-mm-dd hh24:mi:ss'))) as spanDays from dual          --时间差-天
select floor(to_number(sysdate-to_date('1994-09-11 15:55:03','yyyy-mm-dd hh24:mi:ss'))*24) as spanHours from dual         --时间差-时
select floor(to_number(sysdate-to_date('1994-09-11 15:55:03','yyyy-mm-dd hh24:mi:ss'))*24*60) as spanMinutes from dual   	 --时间差-分
select floor(to_number(sysdate-to_date('1994-09-11 15:55:03','yyyy-mm-dd hh24:mi:ss'))*24*60*60) as spanSeconds from dual	 --时间差-秒
```

##### 23 更新时间

​     注:oracle时间加减是以天数为单位,设改变量为n所以换算成年\月\日    

```sql
--改变时间-年
select to_char(sysdate,'yyyy-mm-dd hh24:mi:ss'),to_char(sysdate+n*365,'yyyy-mm-dd hh24:mi:ss') as newTime from dual 
--改变时间-月
select to_char(sysdate,'yyyy-mm-dd hh24:mi:ss'),add_months(sysdate,n) as newTime from dual       --改变时间-日                          
select to_char(sysdate,'yyyy-mm-dd hh24:mi:ss'),to_char(sysdate+n,'yyyy-mm-dd hh24:mi:ss') as newTime from dual
--改变时间-时
select to_char(sysdate,'yyyy-mm-dd hh24:mi:ss'),to_char(sysdate+n/24,'yyyy-mm-dd hh24:mi:ss') as newTime from dual
--改变时间-分
select to_char(sysdate,'yyyy-mm-dd hh24:mi:ss'),to_char(sysdate+n/24/60,'yyyy-mm-dd hh24:mi:ss') as newTime from dual
--改变时间-秒
select to_char(sysdate,'yyyy-mm-dd hh24:mi:ss'),to_char(sysdate+n/24/60/60,'yyyy-mm-dd hh24:mi:ss') as newTime from dual   
```

##### 24 查找月的第一天,最后一天

```sql
 SELECT  Trunc(Trunc(SYSDATE, 'MONTH') - 1, 'MONTH') First_Day_Last_Month,
         Trunc(SYSDATE, 'MONTH') - 1 / 86400 Last_Day_Last_Month,
         Trunc(SYSDATE, 'MONTH') First_Day_Cur_Month,
         LAST_DAY(Trunc(SYSDATE, 'MONTH')) + 1 - 1 / 86400 Last_Day_Cur_Month
  FROM dual;
```

##### 25 增加月份

```plsql
--增加2月
SELECT SYSDATE,add_months(SYSDATE,2) FROM dual;
--减少2月
SELECT SYSDATE,add_months(SYSDATE,-2) FROM dual;
```

26 每年的第一天

```plsql
SELECT to_char(TRUNC(SYSDATE,'yyyy'),'yyyy-mm-dd') mon FROM dual;
```

## 👍笔记

### 1、多字段拼接

​	使用 || 去拼接

```plsql
SELECT *
  FROM (SELECT 
               fc.business_represent_name,
               fc.business_represent_phone,
               fc.business_represent_mail,
               ((SELECT r.description
                  FROM fnd_region_code_vl r
                 WHERE r.region_id = fc.business_province_id)||'-'||
               (SELECT r.description
                  FROM fnd_region_code_vl r
                 WHERE r.region_id = fc.business_city_id)||'-'||
               fc.business_detail_place) companie_address
          FROM fnd_companies_vl fc
         WHERE fc.company_id = '1088522')
```

### 2、NVL、NVL2、**NULLIF**、 **Coalesce** 函数使用

- **NVL**

格式：NVL(expr1,expr2)

含义：如果oracle第一个参数为空那么显示第二个参数的值，如果第一个参数的值不为空，则显示第一个参数本来的值。 

```plsql
t1.approved_by,
(SELECT
   eu.name
 FROM
   hrm_employee_user_v eu
 WHERE
   eu.user_id          = nvl(t1.approved_by,${/session/@user_id}) AND
   eu.primary_user_flag='Y'
 ) approved_by_desc,
```

- **NVL2**

格式：NVL2(expr1,expr2, expr3)

含义：如果该函数的第一个参数不为空那么显示第二个参数的值，如果第一个参数的值为空，则显示第三个参数的值。 

- **NULLIF**

格式：NULLIF(exp1,expr2) 

含义：如果exp1和exp2相等则返回空(NULL)，否则返回第一个值。 

- **Coalesce** 

格式：Coalesce(expr1, expr2, expr3….. exprn) 

含义：表示可以指定多个表达式的占位符。所有表达式必须是相同类型，或者可以隐性转换为相同的类型。 

SELECT COALESCE(NULL,NULL,3,4,5) FROM dual 其返回结果为：3 。实际上是NVL的循环使用 。



### 3 、查看被锁表

```sql
SELECT l.session_id sid,  
           s.serial#,  
           l.locked_mode 锁模式,  
           l.oracle_username 登录用户,  
           l.os_user_name 登录机器用户名,  
           s.machine 机器名,  
           s.terminal 终端用户名,  
           o.object_name 被锁对象名,  
           s.logon_time 登录数据库时间  
    FROM v$locked_object l, all_objects o, v$session s  
    WHERE l.object_id = o.object_id  
       AND l.session_id = s.sid  
    ORDER BY sid, s.serial#;
    
 --解锁
 ALTER system kill session '1775, 45466';
```

### 4、修改密码

​	打开cmd窗口。

```sql
sqlplus / as sysdba ---------以sys登陆          超级用户（sysdba）
alter user 用户名 account unlock; --------- 解除锁定(必须带“;”号)
alter user 用户名 identified by 密码; -------------修改密码
```

### 5、sql技巧

```sql
--获取0-1随机小数
SELECT dbms_random.value FROM dual;
--生成0-100范围内的小数，包括0不包括100
SELECT dbms_random.value(0,100) FROM dual;
--生成0-100范围内的整数，包括0不包括100 
SELECT TRUNC(dbms_random.value(0,100)) FROM dual;
--获取正态分布的随机数
SELECT dbms_random.normal FROM dual;
--生成随机字符串 
/*‘U’ 指生成大写的字符       
‘L’  指生成小写的字符        
‘X’ 数字和大写的字母       
‘A’  大小写混合的字符        
‘P’ 可打印的字符*/
SELECT dbms_random.string('A',8) FROM dual;

--常用处理函数
SELECT CAST('18' AS NUMBER) FROM dual;

/*含义解释：
DECODE(条件,值1,返回值1,值2,返回值2,...值n,返回值n,缺省值)
该函数的含义如下：
IF 条件=值1 THEN
　　　　RETURN(翻译值1)
ELSIF 条件=值2 THEN
　　　　RETURN(翻译值2)
　　　　......
ELSIF 条件=值n THEN
　　　　RETURN(翻译值n)
ELSE
　　　　RETURN(缺省值)
END IF*/
SELECT DECODE('W','Y','是','N','否','-') FROM dual;

--NVL(EXP，返回值1)  当EXP值为null时返回值1，不为null时返回本身   和mysql的IFNULL 函数类似
--NVL2(EXP，返回值1，返回值2)   当exp的值为null时返回值1，不为null时返回值2
SELECT NVL('','赋值') FROM dual;
SELECT NVL2('1','Y','N') t FROM dual;

--从时间抽取年份
SELECT extract(YEAR FROM SYSDATE) FROM dual;
SELECT extract(MONTH FROM SYSDATE) FROM dual;

--保留n位小数
SELECT CAST(11111111.4382312313 AS NUMBER (10, 2) ) as aa FROM dual ;

--数据备份
SELECT table t1_bak AS SELECT * FROM t1;

--闪回查询：10分钟
SELECT * FROM xxx表  AS OF timestamp (systimestamp -10/1440);
          
```



### 6、正则校验

```plsql
--邮箱校验
SELECT COUNT(*)
  FROM dual
 WHERE regexp_like('627261109@qq.com',
                   '^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,4})+$');
```



























