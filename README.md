# Grace College Analysis
### In this analysis, I helped Grace College create a database and analyse students performance based on the student data available
Grace College is an educational institution that recently recognized the need to streamline and enhance their data management processes. They have their dataset in several excel sheets (departments, courses, and students) and now want to move their data into a database.

### Tools
- Excel - Data cleaning
- SQL
  - Database creation
  - Table creation
  - Data Analysis

### Exploratory Data Analysis
EDA involved exploring students records to answer questions, such as;

- Which students have scored higher than the average score of all students?
- How can you convert the numerical scores of students into a textual representation and
display the student's name alongside this textual score?
- Can you categorize students based on their scores?

### Data Analysis

First, I created tables and colums and imported the csv file into SQL. I then did the analysis on SQL.

### Code for creating tables and columns

```sql
CREATE TABLE department (
			department_id VARCHAR (20) PRIMARY KEY,
			department_name VARCHAR (255)
);


CREATE TABLE courses (
			course_id VARCHAR (20) PRIMARY KEY,
			course_name VARCHAR (255),
			course_unit INT,
			department_id VARCHAR (20),
	FOREIGN KEY (department_id) REFERENCES department(department_id)
);


CREATE TABLE scores (
			student_id INT PRIMARY KEY,
			student_name VARCHAR (255),
			course_id VARCHAR (255),
			score DECIMAL (10,2),
	FOREIGN KEY (course_id) REFERENCES courses(course_id)
);
```
#### Queries in SQL

``` SQL
select * from department;
select * from courses;
select * from scores;


-- 1. Categorize Student Scores: How would you categorize student scores into the following?
-- • 90 and above: Excellent
-- • 80 to 89: Very Good
-- • 70 to 79: Good
-- • 60 to 69: Fair
-- • Below 60: Fail

SELECT student_name, score,
	CASE
		WHEN score >= 90 THEN 'Excellent'
		WHEN score between 80 and 89 THEN 'Very Good'
		WHEN score between 70 and 79 THEN 'Good'
		WHEN score between 60 and 69 THEN 'Fair'
		ELSE 'Fail'
	END AS score_category
FROM scores;	

-- 2. Above Average Scorers: Which students have scored higher than the average score of all students?

SELECT student_name, score
FROM scores
WHERE score > (SELECT AVG(score) from scores)
ORDER BY score;

-- 3. Missing Scores: If a student has a missing score
--  for a course, how would you consider it as 0 and display the student's name
-- course, and the adjusted score?

SELECT student_name, course_id, COALESCE(score, 0) AS "Adjusted Score"
FROM scores
ORDER BY "Adjusted Score";

-- 4. Textual Score Representation: How can you convert the numerical scores of students into a 
--textual representation and display the student's name alongside this textual score?

SELECT student_name, CAST(score AS VARCHAR(10)) AS textual score
from scores;

-- -- 5. Student Achievement Levels: Can you categorize students based on their scores as:
-- -- • 90 and Above 90: Excellent Student
-- -- • 70 to 89: Great Student
-- -- • 50 to 69: Very Good Student
-- -- • Below 60: Good Student

SELECT student_name, score,
	CASE
		WHEN score >= 90 THEN 'Excellent Student'
		WHEN score between 70 and 89 THEN 'Great Student'
		WHEN score between 60 and 69 THEN 'Very Good Student'
		ELSE 'Good Student'
	END AS "Student Achievement Level"
FROM scores;

-- 6. Top Scoring Course: Which course has the highest average score among all courses?

SELECT course_id, AVG(score) AS average_score
from scores
GROUP BY course_id 
ORDER BY average_score DESC
LIMIT 1;

-- 7. Student's Top Course: For each student,
-- can you display the course in which they scored the highest? If they haven't taken any
-- courses, display 'No Courses Taken'.

WITH "StudentMaxScores" AS 
	(SELECT student_id, MAX(score) AS max_score
	FROM scores s
	GROUP BY student_id)
SELECT s.student_name, s.score, COALESCE(c.course_name, 'No Courses Taken') AS top_courses
FROM "StudentMaxScores" sms
JOIN scores s
ON sms.student_id = s.student_id AND sms.max_score = s.score
LEFT JOIN courses c
ON s.course_id = c.course_id;

-- 8. Department ID Transformation: How would you convert department IDs
-- to a character data type and display them alongside the department names?

SELECT CAST(department_id AS TEXT), department_name
FROM department;

-- 9. Course Popularity: How can you categorize courses based on the number of students who have taken them? Categories are:
-- • More than 5000 students: Popular
-- • 3000 to 5000 students: Moderate
-- • Fewer than 3 students: Unpopular

SELECT course_id,
	 		CASE	
				WHEN COUNT(student_id) > 5000 THEN 'Popular'
				WHEN COUNT(student_id) between 3000 and 5000 THEN 'Moderate'
				ELSE 'Unpopular'
			END AS "Course Popularity"
FROM scores
GROUP BY course_id;

-- 10. Course Average Scores: What is the average score for each course? If a course has 
-- no scores from students, display the average as 'N/A'

SELECT c.course_id, COALESCE(CAST(AVG(s.score) as VARCHAR(10)), 'N/A') AS average_score
FROM courses c
LEFT JOIN scores s
ON c.course_id = s.course_id
GROUP BY c.course_id;

```

