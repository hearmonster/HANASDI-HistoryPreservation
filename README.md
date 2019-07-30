# HANASDI-HistoryPreservation
Instruction steps to demonstrate SCD Type2 using SDI Transforms (Employee database)

## Step #1 - Build
RMC on "HdbModule" >> Build >> Build

## Step #2 - Create an HDI Connection in the Database Explorer
1. Switch to 'Database Explorer' >>
2.  "+" (button) ('Add a database to the Database Explorer') >>
3. Ensure the 'Database Type' is set to "HDI Container", look for a container starting with "HANASDI-HistoryPreservation-xxxxxxxxxxxxxxxxxxxxxx" >> Select it, and (recommended) rename it to just "HANASDI-HistoryPreservation"

## Step #3 - Demonstrate starting state
Reset data (do all these either through the UI, or from the SQL prompt)
```
CALL "HANASDI_HISTORYPRESERVATION_HDI_HDBMODULE_1"."resetToStartingState"();
```
(Populates the 'emps' "transactional" table, empties the 'emps_history' "Dimension" table and empties the 'EmpsChangeLog' "Change Log")

### Show the "emps" table
```
select * from "Employees.emps";
```

### Show the sequence (you'll need a SQL console for this one)
```
select "EmpsSurrogateGenerator".NEXTVAL from DUMMY;
```
(Run it two or three times to demonstrate the incrementing sequence)


## Step #4
+ Execute Flowgraph
+ Demonstrate EMPLOYEES_HISTORY table (10 records in total)


## Step #5 - Make 5 x alternations to the source table
```
UPDATE "Employees.emps" --demonstrate a preserved update on a watched field (Lastnanme)
SET LNAME = 'Giggs'		--Will appear as an "update" to the original row in the history table, and a new row inserted
WHERE EMP_ID = 1;

UPDATE "Employees.emps" --demonstrate an un-preserved update on an un-watched field (Role)
SET ROLE = 'Demoted-Mgr'
WHERE EMP_ID = 8;

UPDATE "Employees.emps" --demonstrate a preserved update on a watched field (Firstname)
SET FNAME = 'Peter'
WHERE EMP_ID = 6;

DELETE FROM "Employees.emps" --demonstrate a preserved delete
WHERE EMP_ID = 4;				--Will appear as an "update" to the original row in the history table, and marked as inactive

INSERT INTO "Employees.emps" --demonstrate a preserved insert on a new row into the history table
VALUES(20,'Anthony','Rashford','HR Manager');
COMMIT;
```

## STEP #6
+ Execute Flowgraph again
+ Demonstrate changes EMPLOYEES_HISTORY table (3 x updates, 1 x insert, 13 records in total)
