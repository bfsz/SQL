### 1、查询出每门课都大于等于80分的学生姓名

![1559538592160](D:\Documents\GitHub\SQL\images\1559538592160.png)

```plsql
--1
SELECT DISTINCT s.s_name
  FROM student s
 WHERE s.s_name NOT IN
       (SELECT DISTINCT s.s_name FROM student s WHERE s.s_grade < 80);
--2
SELECT s.s_name
  FROM student s
 GROUP BY s.s_name
HAVING MIN(s.s_grade) >= 80;
```

### 2、当成绩大于等于60时，查询结果多一列显示PASS，否则FAIL

```plsql
SELECT s.s_id,
       s.s_name,
       s.s_course,
			 s.s_grade,
       DECODE(SIGN(s.s_grade - 60), 1, 'PASS', -1, 'FAIL') grade
  FROM student s;
```

### 3、为管理业务培训信息，建立3个表：

​     S(S#,SN,SD,SA)S#,SN,SD,SA分别代表学号，学员姓名，所属单位，学员年龄

​     C(C#,CN)C#,CN分别代表课程编号，课程名称

​     SC(S#,C#,G) S#,C#,G分别代表学号，所选的课程编号，学习成绩

**（1）使用标准SQL嵌套语句查询选修课程名称为’税收基础’的学员学号和姓名?**

```plsql
select s# ,sn from s where S# in(select S# from c,sc where c.c#=sc.c# and cn=’税收基础’)
```

 **(2) 使用标准SQL嵌套语句查询选修课程编号为’C2’的学员姓名和所属单位?**

```plsql
select sn,sd from s,sc where s.s#=sc.s# and sc.c#=’c2’
```

  **(3) 使用标准SQL嵌套语句查询不选修课程编号为’C5’的学员姓名和所属单位?**

```plsql
select sn,sd from s where s# not in(select s# from sc where c#=’c5’)
```

 **(4)查询选修了课程的学员人数**

```plsql
select 学员人数=count(distinct s#) from sc
```

 **(5) 查询选修课程超过5门的学员学号和所属单位?**

```plsql
select sn,sd from s where s# in(select s# from sc group by s# having count(distinct c#)>5)
```



### 4、SQL面试题50

Student(stuId,stuName,stuAge,stuSex) 学生表       

stuId：学号；stuName：学生姓名；stuAge：学生年龄；stuSex：学生性别



Course(courseId,courseName,teacherId) 课程表                   

courseId,课程编号；courseName：课程名字；teacherId：教师编号



Scores(stuId,courseId,score) 成绩表    

stuId：学号；courseId,课程编号；score：成绩



Teacher(teacherId,teacherName) 教师表                        

teacherId：教师编号； teacherName：教师名字



**1、查询“001”课程比“002”课程成绩高的所有学生的学号；**

```plsql
  SELECT a.stuid
  FROM (SELECT s.stuid, s.score FROM scores s WHERE s.courseid = 1) a,
       (SELECT s.stuid, s.score FROM scores s WHERE s.courseid = 2) b
 WHERE a.score > b.score
   AND a.stuid = b.stuid;
```

**2、查询平均成绩大于60分的同学的学号和平均成绩；**

```plsql
SELECT s.stuid, AVG(s.score)
  FROM scores s
 GROUP BY s.stuid
HAVING AVG(s.score) > 60;
```

**3、查询所有同学的学号、姓名、选课数、总成绩；**

```plsql
SELECT s.stuid, s.stuname, COUNT(sc.courseid), SUM(sc.score)
  FROM student s
  LEFT OUTER JOIN scores sc
    ON s.stuid = sc.stuid
 GROUP BY s.stuid, s.stuname;
```

**4、查询姓“周”的老师的个数；**

```plsql
SELECT COUNT(DISTINCT(t.teachername))
  FROM teacher t
 WHERE t.teachername LIKE '周%';
```

**5、查询没学过“叶平”老师课的同学的学号、姓名；**

```plsql
SELECT s.stuid, s.stuname
  FROM student s
 WHERE s.stuid NOT IN (SELECT DISTINCT (s.stuid)
                         FROM course c, teacher t, scores s
                        WHERE c.teacherid = t.teacherid
                          AND c.courseid = s.courseid
                          AND t.teachername = '叶平');
```

**6、查询学过“001”并且也学过编号“002”课程的同学的学号、姓名；**

```plsql
SELECT s.stuid, s.stuname
  FROM student s, scores sc
 WHERE s.stuid = sc.stuid
   AND sc.courseid = 1
   AND EXISTS (SELECT *
          FROM scores sco
         WHERE sco.stuid = sc.stuid
           AND sco.courseid = 2);
```

**7、查询学过“叶平”老师所教的所有课的同学的学号、姓名；**

```plsql
  select stuId, stuName
    from Student
   where stuId in
         (select stuId
            from Scores, Course, Teacher
           where Scores.courseId = Course.courseId
             and Teacher.teacherId = Course.teacherId
             and Teacher.teacherName = '叶平'
           group by stuId
          having count(Scores.courseId) = (select count(courseId)
                                            from Course, Teacher
                                           where Teacher.teacherId =
                                                 Course.teacherId
                                             and teacherName = '叶平'));


--如果只教一门课
SELECT s.stuid, s.stuname
  FROM teacher t, course c, student s, scores sc
 WHERE t.teacherid = c.teacherid
   AND c.courseid = sc.courseid
   AND s.stuid = sc.stuid
   AND t.teachername = '叶平';
```

**8、查询课程编号“002”的成绩比课程编号“001”课程低的所有同学的学号、姓名；**

```plsql
 SELECT temp.stuid, temp.stuname
  FROM (SELECT s.stuid,
               s.stuname,
               sc.score score1,
               (SELECT sc.score
                  FROM scores sc
                 WHERE sc.courseid = 2
                   AND s.stuid = sc.stuid) score2
          FROM student s, scores sc
         WHERE s.stuid = sc.stuid
           AND sc.courseid = 1) temp
 WHERE temp.score1 > temp.score2;
```

**9、查询所有课程成绩小于60分的同学的学号、姓名；**

```plsql
SELECT s.stuid, s.stuname
  FROM student s
 WHERE s.stuid NOT IN (SELECT s.stuid
                         FROM student s, scores sc
                        WHERE s.stuid = sc.stuid
                          AND sc.score > 60);
```

**10、查询没有学全所有课的同学的学号、姓名；**

```plsql
SELECT s.stuid, s.stuname
  FROM student s, scores sc
 WHERE s.stuid = sc.stuid
 GROUP BY s.stuid, s.stuname
HAVING COUNT(sc.courseid) < (SELECT COUNT(c.courseid) FROM course c);
```

**11、查询至少有一门课与学号为“1001”的同学所学相同的同学的学号和姓名；**

```plsql
SELECT stuId, stuName
  FROM Student, Scores
 WHERE Student.stuId = Scores.stuId
   AND courseId IN  SELECT courseId FROM Scores WHERE stuId = '1001';
```

**12、查询至少学过学号为“001”同学所有一门课的其他同学学号和姓名；**

```plsql
SELECT DISTINCT s.stuid, s.stuname
  FROM student s, scores sc
 WHERE s.stuid = sc.stuid
   AND sc.courseid IN
       (SELECT sc.courseid FROM scores sc WHERE sc.stuid = 1);
```

**13、把“Scores”表中“叶平”老师教的课的成绩都更改为此课程的平均成绩；**

```plsql
UPDATE scores s
   SET s.score =
       (SELECT AVG(sc.score)
          FROM scores sc
         WHERE s.courseid = (SELECT c.courseid
                               FROM course c, teacher t
                              WHERE t.teacherid = c.teacherid
                                AND t.teachername = '叶平'));
```

**14、查询和“1002”号的同学学习的课程完全相同的其他同学学号和姓名；**

```plsql
SELECT s.stuid
  FROM scores s
 WHERE s.courseid in (SELECT s.courseid FROM scores s WHERE s.stuid = 2)
 GROUP BY s.stuid
HAVING COUNT(*) = (SELECT COUNT(*) FROM scores s WHERE s.stuid = 2);
```

**15、删除学习“叶平”老师课的Scores表记录；**

```plsql
DELETE FROM scores s
 WHERE s.courseid = (SELECT c.courseid
                       FROM course c, teacher t
                      WHERE t.teacherid = c.teacherid
                        AND t.teachername = '叶平');
```

**16、向Scores表中插入一些记录，这些记录要求符合以下条件：没有上过编号“003”课程的同学学号、2号课的平均成绩；**

    
    
    Insert Scores select stuId,'002',(Select avg(score)
    
    from Scores where courseId='002') from Student where stuId not in (Select stuId from Scores where courseId='002');

17、按平均成绩从高到低显示所有学生的“数据库”、“企业管理”、“英语”三门的课程成绩，按如下形式显示： 学生ID,,数据库,企业管理,英语,有效课程数,有效平均分

    SELECT stuId as 学生ID
    
        ,(SELECT score FROM Scores WHERE Scores.stuId=t.stuId AND courseId='004') AS 数据库
    
        ,(SELECT score FROM Scores WHERE Scores.stuId=t.stuId AND courseId='001') AS 企业管理
    
        ,(SELECT score FROM Scores WHERE Scores.stuId=t.stuId AND courseId='006') AS 英语
    
        ,COUNT(*) AS 有效课程数, AVG(t.score) AS 平均成绩
    
    FROM Scores AS t
    
    GROUP BY stuId
    
    ORDER BY avg(t.score) 

18、查询各科成绩最高和最低的分：以如下形式显示：课程ID，最高分，最低分

    SELECT L.courseId As 课程ID,L.score AS 最高分,R.score AS 最低分
    
    FROM Scores L ,Scores AS R
    
    WHERE L.courseId = R.courseId and
    
        L.score = (SELECT MAX(IL.score)
    
                      FROM Scores AS IL,Student AS IM
    
                      WHERE L.courseId = IL.courseId and IM.stuId=IL.stuId
    
                      GROUP BY IL.courseId)
    
        AND
    
        R.score = (SELECT MIN(IR.score)
    
                      FROM Scores AS IR
    
                      WHERE R.courseId = IR.courseId
    
                  GROUP BY IR.courseId
    
                    );

19、按各科平均成绩从低到高和及格率的百分数从高到低顺序

    SELECT t.courseId AS 课程号,max(course.courseName)AS 课程名,isnull(AVG(score),0) AS 平均成绩
    
        ,100 * SUM(CASE WHEN  isnull(score,0)>=60 THEN 1 ELSE 0 END)/COUNT(*) AS 及格百分数
    
    FROM Scores T,Course
    
    where t.courseId=course.courseId
    
    GROUP BY t.courseId
    
    ORDER BY 100 * SUM(CASE WHEN  isnull(score,0)>=60 THEN 1 ELSE 0 END)/COUNT(*) DEScores

20、查询如下课程平均成绩和及格率的百分数(用"1行"显示): 企业管理（001），马克思（002），OO&UML （003），数据库（004）

    SELECT SUM(CASE WHEN courseId ='001' THEN score ELSE 0 END)/SUM(CASE courseId WHEN '001' THEN 1 ELSE 0 END) AS 企业管理平均分
    
        ,100 * SUM(CASE WHEN courseId = '001' AND score >= 60 THEN 1 ELSE 0 END)/SUM(CASE WHEN courseId = '001' THEN 1 ELSE 0 END) AS 企业管理及格百分数
    
        ,SUM(CASE WHEN courseId = '002' THEN score ELSE 0 END)/SUM(CASE courseId WHEN '002' THEN 1 ELSE 0 END) AS 马克思平均分
    
        ,100 * SUM(CASE WHEN courseId = '002' AND score >= 60 THEN 1 ELSE 0 END)/SUM(CASE WHEN courseId = '002' THEN 1 ELSE 0 END) AS 马克思及格百分数
    
        ,SUM(CASE WHEN courseId = '003' THEN score ELSE 0 END)/SUM(CASE courseId WHEN '003' THEN 1 ELSE 0 END) AS UML平均分
    
        ,100 * SUM(CASE WHEN courseId = '003' AND score >= 60 THEN 1 ELSE 0 END)/SUM(CASE WHEN courseId = '003' THEN 1 ELSE 0 END) AS UML及格百分数
    
        ,SUM(CASE WHEN courseId = '004' THEN score ELSE 0 END)/SUM(CASE courseId WHEN '004' THEN 1 ELSE 0 END) AS 数据库平均分
    
        ,100 * SUM(CASE WHEN courseId = '004' AND score >= 60 THEN 1 ELSE 0 END)/SUM(CASE WHEN courseId = '004' THEN 1 ELSE 0 END) AS 数据库及格百分数

  FROM Scores

21、查询不同老师所教不同课程平均分从高到低显示

  SELECT max(Z.teacherId) AS 教师ID,MAX(Z.teacherName) AS 教师姓名,C.courseId AS 课程ＩＤ,MAX(C.courseName) AS 课程名称,AVG(score) AS 平均成绩

    FROM Scores AS T,Course AS C ,Teacher AS Z
    
    where T.courseId=C.courseId and C.teacherId=Z.teacherId

  GROUP BY C.courseId

  ORDER BY AVG(score) DEScores

22、查询如下课程成绩第 3 名到第 6 名的学生成绩单：企业管理（001），马克思（002），UML （003），数据库（004）

    [学生ID],[学生姓名],企业管理,马克思,UML,数据库,平均成绩
    
    SELECT  DISTINCT top 3
    
      Scores.stuId As 学生学号,
    
        Student.stuName AS 学生姓名 ,
    
      T1.score AS 企业管理,
    
      T2.score AS 马克思,
    
      T3.score AS UML,
    
      T4.score AS 数据库,
    
      ISNULL(T1.score,0) + ISNULL(T2.score,0) + ISNULL(T3.score,0) + ISNULL(T4.score,0) as 总分
    
      FROM Student,Scores  LEFT JOIN Scores AS T1
    
                      ON Scores.stuId = T1.stuId AND T1.courseId = '001'
    
            LEFT JOIN Scores AS T2
    
                      ON Scores.stuId = T2.stuId AND T2.courseId = '002'
    
            LEFT JOIN Scores AS T3
    
                      ON Scores.stuId = T3.stuId AND T3.courseId = '003'
    
            LEFT JOIN Scores AS T4
    
                      ON Scores.stuId = T4.stuId AND T4.courseId = '004'
    
      WHERE student.stuId=Scores.stuId and
    
      ISNULL(T1.score,0) + ISNULL(T2.score,0) + ISNULL(T3.score,0) + ISNULL(T4.score,0)
    
      NOT IN
    
      (SELECT
    
            DISTINCT
    
            TOP 15 WITH TIES
    
            ISNULL(T1.score,0) + ISNULL(T2.score,0) + ISNULL(T3.score,0) + ISNULL(T4.score,0)
    
      FROM Scores
    
            LEFT JOIN Scores AS T1
    
                      ON Scores.stuId = T1.stuId AND T1.courseId = 'k1'
    
            LEFT JOIN Scores AS T2
    
                      ON Scores.stuId = T2.stuId AND T2.courseId = 'k2'
    
            LEFT JOIN Scores AS T3
    
                      ON Scores.stuId = T3.stuId AND T3.courseId = 'k3'
    
            LEFT JOIN Scores AS T4
    
                      ON Scores.stuId = T4.stuId AND T4.courseId = 'k4'
    
      ORDER BY ISNULL(T1.score,0) + ISNULL(T2.score,0) + ISNULL(T3.score,0) + ISNULL(T4.score,0) DEScores);

23、统计列印各科成绩,各分数段人数:课程ID,课程名称,[100-85],[85-70],[70-60],[ <60]

    SELECT Scores.courseId as 课程ID, courseName as 课程名称
    
        ,SUM(CASE WHEN score BETWEEN 85 AND 100 THEN 1 ELSE 0 END) AS [100 - 85]
    
        ,SUM(CASE WHEN score BETWEEN 70 AND 85 THEN 1 ELSE 0 END) AS [85 - 70]
    
        ,SUM(CASE WHEN score BETWEEN 60 AND 70 THEN 1 ELSE 0 END) AS [70 - 60]
    
        ,SUM(CASE WHEN score < 60 THEN 1 ELSE 0 END) AS [60 -]
    
    FROM Scores,Course
    
    where Scores.courseId=Course.courseId
    
    GROUP BY Scores.courseId,courseName;

24、查询学生平均成绩及其名次

      SELECT 1+(SELECT COUNT( distinct 平均成绩)
    
              FROM (SELECT stuId,AVG(score) AS 平均成绩
    
                      FROM Scores
    
                  GROUP BY stuId
    
                  ) AS T1
    
            WHERE 平均成绩 > T2.平均成绩) as 名次,
    
      stuId as 学生学号,平均成绩
    
    FROM (SELECT stuId,AVG(score) 平均成绩
    
            FROM Scores
    
        GROUP BY stuId
    
        ) AS T2
    
    ORDER BY 平均成绩 deScores;

 



25、查询各科成绩前三名的记录:(不考虑成绩并列情况)

      SELECT t1.stuId as 学生ID,t1.courseId as 课程ID,score as 分数
    
      FROM Scores t1
    
      WHERE score IN (SELECT TOP 3 score
    
              FROM Scores
    
              WHERE t1.courseId= courseId
    
            ORDER BY score DEScores
    
              )
    
      ORDER BY t1.courseId;

26、查询每门课程被选修的学生数

  select courseId,count(stuId) from Scores group by courseId;

27、查询出只选修了一门课程的全部学生的学号和姓名

  select Scores.stuId,Student.stuName,count(courseId) AS 选课数

  from Scores ,Student

  where Scores.stuId=Student.stuId group by Scores.stuId ,Student.stuName having count(courseId)=1;

28、查询男生、女生人数

    Select count(stuSex) as 男生人数 from Student group by stuSex having stuSex='男';
    
    Select count(stuSex) as 女生人数 from Student group by stuSex having stuSex='女'；

29、查询姓“张”的学生名单

    SELECT stuName FROM Student WHERE stuName like '张%';

30、查询同名同性学生名单，并统计同名人数

  select stuName,count(*) from Student group by stuName having  count(*)>1;;

31、1981年出生的学生名单(注：Student表中stuAge列的类型是datetime)

    select stuName,  CONVERT(char (11),DATEPART(year,stuAge)) as age
    
    from student
    
    where  CONVERT(char(11),DATEPART(year,stuAge))='1981';

32、查询每门课程的平均成绩，结果按平均成绩升序排列，平均成绩相同时，按课程号降序排列

    Select courseId,Avg(score) from Scores group by courseId order by Avg(score),courseId DEScores ;

33、查询平均成绩大于85的所有学生的学号、姓名和平均成绩

    select stuName,Scores.stuId ,avg(score)
    
    from Student,Scores
    
    where Student.stuId=Scores.stuId group by Scores.stuId,stuName having    avg(score)>85;

34、查询课程名称为“数据库”，且分数低于60的学生姓名和分数

    Select stuName,isnull(score,0)
    
    from Student,Scores,Course
    
    where Scores.stuId=Student.stuId and Scores.courseId=Course.courseId and  Course.courseName='数据库'and score <60;

35、查询所有学生的选课情况；

    SELECT Scores.stuId,Scores.courseId,stuName,courseName
    
    FROM Scores,Student,Course
    
    where Scores.stuId=Student.stuId and Scores.courseId=Course.courseId ;

36、查询任何一门课程成绩在70分以上的姓名、课程名称和分数；

    SELECT  distinct student.stuId,student.stuName,Scores.courseId,Scores.score
    
    FROM student,Scores
    
    WHERE Scores.score>=70 AND Scores.stuId=student.stuId;

37、查询不及格的课程，并按课程号从大到小排列

    select courseId from Scores where Scoresor e <60 order by courseId ;

38、查询课程编号为003且课程成绩在80分以上的学生的学号和姓名；

    select Scores.stuId,Student.stuName from Scores,Student where Scores.stuId=Student.stuId and score>80 and courseId='003';

39、求选了课程的学生人数

    select count(*) from Scores;

40、查询选修“叶平”老师所授课程的学生中，成绩最高的学生姓名及其成绩

    select Student.stuName,score
    
    from Student,Scores,Course C,Teacher
    
    where Student.stuId=Scores.stuId and Scores.courseId=C.courseId and C.teacherId=Teacher.teacherId and Teacher.teacherName='叶平' and Scores.score=(select max(score)from Scores where courseId=C.courseId );

41、查询各个课程及相应的选修人数

    select count(*) from Scores group by courseId;

42、查询不同课程成绩相同的学生的学号、课程号、学生成绩

  select distinct  A.stuId,B.score from Scores A  ,Scores B where A.score=B.score and A.courseId <>B.courseId ;

43、查询每门功成绩最好的前两名

    SELECT t1.stuId as 学生ID,t1.courseId as 课程ID,score as 分数
    
      FROM Scores t1
    
      WHERE score IN (SELECT TOP 2 score
    
              FROM Scores
    
              WHERE t1.courseId= courseId
    
            ORDER BY score DEScores
    
              )
    
      ORDER BY t1.courseId;

44、统计每门课程的学生选修人数（超过10人的课程才统计）。要求输出课程号和选修人数，查询结果按人数降序排列，查询结果按人数降序排列，若人数相同，按课程号升序排列 

    select  courseId as 课程号,count(*) as 人数
    
    from  Scores 
    
    group  by  courseId
    
    order  by  count(*) deScores,courseId 

45、检索至少选修两门课程的学生学号

    select  stuId 
    
    from  Scores 
    
    group  by  stuId
    
    having  count(*)  >  =  2

46、查询全部学生都选修的课程的课程号和课程名

    select  courseId,courseName 
    
    from  Course 
    
    where  courseId  in  (select  courseId  from  Scores group  by  courseId) 

47、查询没学过“叶平”老师讲授的任一门课程的学生姓名

    select stuName from Student where stuId not in (select stuId from Course,Teacher,Scores where Course.teacherId=Teacher.teacherId and Scores.courseId=course.courseId and teacherName='叶平');

48、查询两门以上不及格课程的同学的学号及其平均成绩

    select stuId,avg(isnull(score,0)) from Scores where stuId in (select stuId from Scores where score <60 group by stuId having count(*)>2)group by stuId;

49、检索“004”课程分数小于60，按分数降序排列的同学学号

    select stuId from Scores where courseId='004'and score <60 order by score deScores;

50、删除“002”同学的“001”课程的成绩

delete from Scores where stuId='002'and courseId='001';