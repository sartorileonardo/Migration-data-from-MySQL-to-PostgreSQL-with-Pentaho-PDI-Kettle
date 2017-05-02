# Migration-data-from-MySQL-to-PostgreSQL-with-Pentaho-PDI-Kettle
This project is a laboratory in which MySQL Data Migration for PostgreSQL with Pentaho PDI (Kettle) was performed.  All the steps in the Tutorial are available in Readme and the transformation is available for Download.

Steps:

Note: Before you begin, make sure that you have Java JDK 1.7 installed on your computer and that your environment variables are correctly configured.

1. Download Pentaho PDI (Kettle) version 5.0.1-stable at:
Https://sourceforge.net/projects/pentaho/files/Data%20Integration/5.0.1-stable/

2. Extract the compressed file.

3. Download JDBC libs MySQL and PostgreSQL
- Download Postgres JDBC: https://jdbc.postgresql.org/download.html
- Download MySQL JDBC: https://dev.mysql.com/downloads/connector/j/

4. Download the Sakila-db database with the "wget ​​http://downloads.mysql.com/docs/sakila-db.tar.gz" command on the terminal if you are using Linux, or access the link if you are using Windows . The database is also available in the repository for this data migration project.

5. Import DB Sakila to MySQL and create the DB structure.

6. Create the new database in Postgres to which we will migrate the data, I recommend using the PgAdmin tool. Here are details of the new DB:
> Name: customer_db
> Table: customer_tb
> Columns:
> Customer_id (serial)
> Full_name (text)
> Active (boolean)
> Email (text)

Note: Do not forget to give write privileges to DB and TB in properties.

7. Copy the JDBC libs and paste inside the "data-integration / lib" directory

8. Run the "spoon.sh" file inside the "data-itegration /" home directory if you are using linux with the command "sh spoon.sh" or if you are using Windows, then run "spoon.bat".

9. After opening the POI, in the upper left-hand tab of the screen called "View" you will find an option called "Transformations", then on top of this folder, right-click and choose "New".

10. In the upper left corner of the screen go to the View> Transformations> Connections> Right> New and fill in the data:
> Connection Name: Sakila_MySQL
> Connection Type: MySQL
> Host name: localhost or BD IP
> Database name: sakila
> Port number: 3306
> User name: <user>
> Password: <password>
> Access: Native (JDBC)

Use the Test button to validate the connection, if errors occur, check the previous steps. If the message Ok appears, click OK and continue with the remaining steps.

Note: If you want to make future Transformations with these connections, just right click> Share.

11. Do the same procedure (step 10) above to create the connection with the DB "customer_db" in Postgres, but with the data of your Postgres connection.

12. On the "Design" tab located in the upper left corner of the screen there are several components available, so within the Input subcategory, select the Input Table component and drag it to the whiteboard on the right. This component will be responsible for querying the data we want that is located in MySQL.

13. When opening the component with a double click, fill in the information:
> Step name: Table input MySQL
> Connection: Sakila_MySQL
> SQL: SELECT customer_id, first_name, last_name, email, active FROM customer

Click the Preview button and if everything is set correctly you will choose the number of records and will open a new window with the query data.

14. Now let's go to the data validation step, where you will create your error alerts for non-standard data that you want.
Then select the "Data Validator" component located under Design> Validation. Drag the component next to the 1st step "input", hover the mouse over the component and click the icon where there is a document with a green arrow pointing to the right and connect by dropping the arrow on top of the second component, the Data Validator.

15. In the Data Validator component create new validations for each SELECT column, for example name validation: Click on the "New Validation" button> fill in the data as follows:
> Validation description: first_name
> Name of field to value: first_name
> Error code: 01
> Error description: Error in field first_name

In the Fieldset Type below, set the checkBox "Verify data type" to true so that the data type is checked and below select the data type to check, in case name is String.

In the Data fieldset below, leave the option "Null allowed" checked, this option indicates that the validation will allow NULL data in this column which by default is not mandatory, note that in the field that is mandatory, it must be unchecked.

Leave unchecked the "Verify data type" option for email.

Note: In "Max string length" and "Min string length" it is still possible to set the maximum / minimum size of the field in integer, in which case I will leave it blank.

16. Use the same logic as step 15 to create the validations of the other columns by incrementing the "Error Code" by +1 for each column.

17. The next Step to insert is String Operations that has the function of removing spaces from the beginning / end of a text field (Trim) and up to special characters or spaces in the middle of the text. After dragging the component into the workspace (whiteboard), connect the Data Validator sequentially as your next Stepe change your Step Name to "Remove Spaces".

Configure it by clicking the "Get Faithful" button, after loading the fields select the "both" option for all the lines in the "Trim type" column, this will remove spaces from the beginning and end of the fields.

For the "email" field, also select the "lower" value in the Lower / Upper column to transform emails from uppercase to lowercase and change the value of the column "Remove Special character" to space, this will cause it to eliminate spaces in the middle Of the e-mail text.

18. Select step "Table output" to write the data to the destination and connect it sequentially in step "Remove spaces".
Set the "Table output" step with the following data:
> Step Name: Table output Postgres
> Connection: Customer_DB_Postgres
> Target schema: public (Can be chosen with browse button)
> Target table: customer_tb (Can be chosen with browse button)
> Specify database fields (mark this checkBox as true)

In the tab below called "Database fiels" you can manually select the fields of the left column (destination) and the corresponding ones from the right (Source) or even do automatic mapping (Not 100% accurate) by clicking the "Get fields" button and Followed by the "OK" button to finish setting up this Step.

After this mapping, you should save the transformation (Ctrl + S) and execute it on the green (Play) button in the button bar located in the upper left corner of the screen.

If you have configured everything correctly and there is no inconsistency, Pentaho Kettle will display a new tab called "Execution Results" with the data:
=================================================================================================================
Step name Copy nr Written read Input Output Updated Rejected Errors Active Time Speed ​​(r / s) Pri / ent / exit
Table input MySQL 0 0 599 599 0 0 0 0 Finished 0.0s 13,022 -
Data Validator 0 599 599 0 0 0 0 0 Finished 0.0s 26,043 -
Remove Spaces 0 599 599 0 0 0 0 0 Finished 0.0s 13.022 -
Table output Postgres 0 599 599 0 599 0 0 0 Finished 0.1s 4,792 -
=================================================================================================================

Conclusion
Soon, 599 records of Sakila in MySQL were migrated to customer_db in Postgres in just 0.1s of time and without the need for advanced knowledge in both DBMS.

If you have successfully completed, congratulations and until the next LAB.
