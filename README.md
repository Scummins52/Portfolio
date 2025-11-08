# Vacation Reviews — Database README

Overview
--------
This repository contains SQL used to create and populate a small personal database called `VACATION_REVIEWS`. The database stores countries visited, destinations within those countries, visit dates, landmarks, and cuisines along with review scores (1–10). It’s intended as a simple travel log and review tracker.

This README documents the schema, how to create/load the database, example queries, and suggestions for future improvements.

Quick start
-----------
1. Open your MySQL client (or compatible server).
2. Run the SQL file that creates the schema and inserts sample records. Example:
```sql
-- Creates the database and schema
CREATE DATABASE VACATION_REVIEWS;
USE VACATION_REVIEWS;

-- (run the remainder of the SQL script in this folder)
```
3. Verify the tables:
```sql
SHOW TABLES;
SELECT * FROM COUNTRY;
SELECT * FROM DESTINATION;
SELECT * FROM VACATION_DATE;
SELECT * FROM LANDMARK;
SELECT * FROM CUISINE;
```

Schema (tables & columns)
-------------------------
Note: types and constraints shown are as in the provided SQL script.

- COUNTRY
  - id INT PRIMARY KEY AUTO_INCREMENT
  - COUNTRY_NAME VARCHAR(20)
  - CONTINENT ENUM('NORTH AMERICA','SOUTH AMERICA','EUROPE','AFRICA','ASIA','AUSTRALIA','ANTARTICA')

- DESTINATION
  - id INT PRIMARY KEY AUTO_INCREMENT
  - DESTINATION_NAME VARCHAR(20)
  - COUNTRY_ID INT — foreign key → COUNTRY(id)
  - REVIEW_SCORE INT — CHECK(REVIEW_SCORE BETWEEN 1 AND 10)

- VACATION_DATE
  - id INT PRIMARY KEY AUTO_INCREMENT
  - COUNTRY_ID INT — foreign key → COUNTRY(id)
  - DESTINATION_ID INT — (added later in script) foreign key → DESTINATION(id)
  - ARRIVE_DATE DATE
  - DEPART_DATE DATE

- LANDMARK
  - id INT PRIMARY KEY AUTO_INCREMENT
  - LANDMARK_NAME VARCHAR(30)
  - DESTINATION_ID INT — foreign key → DESTINATION(id)
  - LANDMARK_REVIEW INT — CHECK(LANDMARK_REVIEW BETWEEN 1 AND 10)

- CUISINE
  - id INT PRIMARY KEY AUTO_INCREMENT
  - CUISINE_NAME VARCHAR(30)
  - FOOD_TYPE ENUM('BREAKFAST','LUNCH','DINNER','DESSERT') — later dropped in script
  - COUNTRY_ID INT — foreign key → COUNTRY(id)
  - FOOD_REVIEW INT — CHECK(FOOD_REVIEW BETWEEN 1 AND 10)

Constraints & notes
-------------------
- Review scores (destination, landmark, food) are constrained to be between 1 and 10 via CHECK clauses where used.
- Some operations in the SQL include ALTER TABLE to add/modify columns and TRUNCATE operations (they remove all data). Use caution when running those commands.
- The script demonstrates iterative development: adding DESTINATION_ID to VACATION_DATE, dropping a column (food_type), truncating tables, and updating rows.
- The script uses literal date values in YYYY-MM-DD format for ARRIVE_DATE and DEPART_DATE.

Example queries
---------------
- List all countries:
```sql
SELECT * FROM COUNTRY;
```

- List destinations with country names and scores:
```sql
SELECT d.id, d.DESTINATION_NAME, c.COUNTRY_NAME, d.REVIEW_SCORE
FROM DESTINATION d
JOIN COUNTRY c ON d.COUNTRY_ID = c.id
ORDER BY d.REVIEW_SCORE DESC;
```

- All landmarks for a destination:
```sql
SELECT l.LANDMARK_NAME, l.LANDMARK_REVIEW
FROM LANDMARK l
WHERE l.DESTINATION_ID = <destination_id>;
```

- Travel history (dates joined to destination and country):
```sql
SELECT vd.arrive_date, vd.depart_date, dest.DESTINATION_NAME, c.COUNTRY_NAME
FROM VACATION_DATE vd
LEFT JOIN DESTINATION dest ON vd.DESTINATION_ID = dest.id
LEFT JOIN COUNTRY c ON vd.COUNTRY_ID = c.id
ORDER BY vd.arrive_date;
```

- Average review per country (destinations):
```sql
SELECT c.COUNTRY_NAME, AVG(d.REVIEW_SCORE) AS avg_score
FROM DESTINATION d
JOIN COUNTRY c ON d.COUNTRY_ID = c.id
GROUP BY c.id, c.COUNTRY_NAME
ORDER BY avg_score DESC;
```

- Top-rated cuisines:
```sql
SELECT cuisine_name, food_review, COUNTRY_ID
FROM CUISINE
ORDER BY food_review DESC
LIMIT 10;
```

Sample data actions present in script
-----------------------------------
- Insertion examples for COUNTRY, DESTINATION, LANDMARK, CUISINE, VACATION_DATE are included in the SQL file.
- The script also contains TRUNCATE TABLE operations and UPDATEs; these modify or clear sample data. Review and edit those lines if you want to keep sample data.

Practical tips
--------------
- Back up data before running scripts that contain TRUNCATE or destructive UPDATE statements.
- If your MySQL version enforces stricter CHECK support, validate whether CHECK clauses are enforced or need additional handling.
- Consider adding indexes for faster JOINs on foreign keys (e.g., DESTINATION.COUNTRY_ID, VACATION_DATE.DESTINATION_ID).
- If you want multi-user or per-review attribution, add a USERS table and link reviews to user IDs.

Suggested improvements
----------------------
- Normalize the CONTINENT values into a separate CONTINENT table and reference by id.
- Add timestamps (created_at, updated_at) to review rows for auditing.
- Add a USERS table (id, name, email) if you want to record which user added which review.
- Add ON DELETE/ON UPDATE behaviors to foreign keys (CASCADE, SET NULL) to control referential changes.
- Expand text lengths where necessary (e.g., country or destination names longer than current VARCHAR limits).
- Add constraints to ensure ARRIVE_DATE <= DEPART_DATE in VACATION_DATE (CHECK or application logic).

Author / Contact
----------------
- Database & script: Scummins52 (owner of this repo)
- For questions about the schema or sample data, open an issue in the repository or contact the repo owner.

License
-------
This repository follows the license declared at the repository root (if any). Check the main repo for licensing details.

Enjoy exploring your travel history and reviews!
