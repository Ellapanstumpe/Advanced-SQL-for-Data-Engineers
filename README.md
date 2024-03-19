# Advanced-SQL-for-Data-Engineers

This project is a Final Project: Advanced SQL Techniques of a Honors module from Databases and SQL for Data Science with Python course. It's also one of IBM Professional Data Engineer
Certification Projects. In this project, I used the phpMyAdmin GUI tool to write MySQL. You also feel free to use MYSQL WORKBENCH. Please refer to the PROJECT QUESTIONS.pdf file to 
find the project requirements and details. 


## Installation

Please download the following dump files to your local machine, then import to your database on your phpMyAdmin or MYSQL WORKBENCH.

chicago_crime.sql\
chicago_public_schools.sql\
chicago_socioeconomic_data.sql\

You can find my solutions for all of the questions about the project from my repositories. 
For your convenance,  following code is for the solutions on Exercise 4: Using Transactions




DROP PROCEDURE IF EXISTS UPDATE_LEADERS_SCORE;
DELIMITER $$
CREATE PROCEDURE UPDATE_LEADERS_SCORE(IN in_School_ID INT , IN in_Leader_Score INT )
BEGIN
    DECLARE rollback_occurred BOOLEAN DEFAULT FALSE;
    
    -- Declare handler to catch exceptions
    DECLARE CONTINUE HANDLER FOR SQLEXCEPTION
    BEGIN
        SET rollback_occurred = TRUE;
        ROLLBACK;
        RESIGNAL;
    END;
    
    START TRANSACTION;

    UPDATE chicago_public_schools
    SET Leaders_Score = in_Leader_Score
    WHERE School_ID = in_School_ID;

    IF in_Leader_Score >= 0 AND in_Leader_Score < 20 THEN
        UPDATE chicago_public_schools
        SET Leaders_Icon = 'Very weak'
        WHERE School_ID = in_School_ID ;
    
    ELSEIF  in_Leader_Score < 40 THEN
        UPDATE chicago_public_schools
        SET Leaders_Icon = 'Weak'
        WHERE School_ID = in_School_ID ;
    
    ELSEIF in_Leader_Score < 60 THEN
        UPDATE chicago_public_schools
        SET Leaders_Icon = 'Average'
        WHERE School_ID = in_School_ID ;
    
    ELSEIF in_Leader_Score < 80 THEN
        UPDATE chicago_public_schools
        SET Leaders_Icon = 'Strong'
        WHERE School_ID = in_School_ID ;
    
    ELSEIF in_Leader_Score < 100 THEN
        UPDATE chicago_public_schools
        SET Leaders_Icon = 'Very Strong'
        WHERE School_ID = in_School_ID ;
    
    ELSE
         SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Invalid value for in_Leader_Score. Value must be between 0 and 99.';
END IF;
IF rollback_occurred THEN
        ROLLBACK;
    ELSE 
        COMMIT;     
END IF; 
END $$
DELIMITER ;

CALL UPDATE_LEADERS_SCORE(609993, 38);
SELECT School_ID, Leaders_Score, Leaders_Icon FROM chicago_public_schools 
WHERE School_ID = 609993;

CALL UPDATE_LEADERS_SCORE(609993, 101);
