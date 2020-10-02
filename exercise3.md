#Exercise 3 - Standup
##Daniel Sause 2020-10-02

##Setup

###run mysql:
mysql -u root -p 
(then enter your password)

###run the script to create mysql database:
\. /home/student/Desktop/standup.sql

###create database in postgresql:
createdb standup

##convert mysql database to postgresql database:

###install pgloader:
sudo apt install pgloader

###verify it installed:
pgloader --version

###convert:
pgloader mysql://root:student@localhost/standup pgsql:///standup

###Open the database in psql:
psql -d standup

##Run your queries!

SELECT language.language AS "First Language" 
  FROM standup.language language 
    JOIN standup.userlang userlang on userlang.language_id = language.language_id 
GROUP BY language.language 
ORDER BY count(language.language) DESC LIMIT 1;

 First Language 
----------------
 JAVA
(1 row)

SELECT language.language, skill.value, count(language.language) 
  FROM standup.language language 
    JOIN standup.userlang userlang ON userlang.language_id = language.language_id 
    JOIN standup.skill skill ON skill.skill_id = userlang.skill_level 
GROUP BY language.language, skill.value 
ORDER BY language.language, 
  CASE skill.value 
    WHEN 'ULTRA LOW' THEN 1 
    WHEN 'LOW' THEN 2 
    WHEN 'MEDIUM' 
    THEN 3 WHEN 'HIGH' 
    THEN 4 
END DESC;

  language  |   value   | count 
------------+-----------+-------
 C          | HIGH      |     1
 C          | MEDIUM    |     2
 C          | LOW       |     7
 C#         | HIGH      |     1
 C#         | MEDIUM    |     2
 C#         | LOW       |     2
 C++        | MEDIUM    |    22
 C++        | LOW       |     4
 COBOL      | MEDIUM    |     1
 JAVA       | HIGH      |    34
 JAVA       | MEDIUM    |     1
 JAVASCRIPT | HIGH      |     1
 JAVASCRIPT | MEDIUM    |     4
 OTHER      | HIGH      |     1
 OTHER      | MEDIUM    |     3
 OTHER      | LOW       |     7
 OTHER      | ULTRA LOW |     2
 PHP        | HIGH      |     1
 PHP        | MEDIUM    |     3
 PHP        | LOW       |     3
 PYTHON     | HIGH      |     1
 PYTHON     | MEDIUM    |     1
 PYTHON     | LOW       |     2
(23 rows)


SELECT userlang.student_id,
  MAX(CASE
    WHEN skill.VALUE = 'HIGH' THEN language.language
  END) AS "HIGH",
  MAX(CASE
    WHEN skill.VALUE = 'MEDIUM' THEN language.language
  END) AS "MEDIUM"
FROM standup.userlang userlang
  JOIN standup.language lang ON language.language_id = userlang.language_id
  JOIN standup.skill skill ON skill.value IN ('HIGH', 'MEDIUM') AND skill.skill_id = userlang.skill_level
GROUP BY userlang.student_id 
ORDER BY userlang.student_id;


 student_id |    HIGH    |   MEDIUM   
------------+------------+------------
          1 | JAVA       | C++
          2 | JAVA       | C++
          3 | JAVA       | C++
          4 | JAVA       | C++
          5 | JAVA       | C++
          6 | JAVA       | C++
          7 | JAVA       | C++
          8 | JAVA       | C++
          9 | JAVA       | C++
         10 | JAVA       | C++
         11 | JAVA       | PYTHON
         12 | JAVA       | C++
         13 | JAVA       | C++
         14 | JAVA       | C++
         15 | JAVA       | C#
         16 | OTHER      | 
         17 | JAVA       | JAVASCRIPT
         18 | PHP        | OTHER
         19 | JAVASCRIPT | PHP
         20 | C          | C++
         21 | PYTHON     | JAVA
         22 | JAVA       | JAVASCRIPT
         23 | JAVA       | OTHER
         24 | JAVA       | PHP
         25 | JAVA       | C
         26 | JAVA       | PHP
         27 | JAVA       | C#
         28 | JAVA       | COBOL
         29 | JAVA       | C++
         30 | JAVA       | C++
         31 | JAVA       | C++
         32 | JAVA       | C++
         33 | JAVA       | C++
         34 | JAVA       | JAVASCRIPT
         35 | C#         | C++
         36 | JAVA       | JAVASCRIPT
         37 | JAVA       | C++
         38 | JAVA       | C
         39 | JAVA       | C++
         40 | JAVA       | OTHER
(40 rows)
