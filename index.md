# How to Integrate SQLite3 with Java
 
### Introduction to SQLite3
 
SQLite is a very popular system for implementing simple relational databases. These databases allow us to access, modify, and query data in a structured way. However, often we want to use a relational database within another application. In this tutorial, we will demonstrate how to integrate a simple SQLite3 database into a Java application for new and old users alike.
 
To install SQLite3, follow the directions at [this link](http://www.sqlitetutorial.net/download-install-sqlite/).
 
### Design Database Tables
 
Let's say we want to model students with a relational database schema. Each column would represent one property or attribute of a student. Each row in the table would represent an individual student. In the example below we have a table containing students with the attributes ID, first name, last name, and grade. The student with the ID of 1 has the first name Sarah, the last name Hall, and is in the grade 9.


| ID    | FirstName | LastName | Grade |
| ----- | --------- | -------- | ----- |
| 0     | Jerad     | Hoy      |  10   |
| 1     | Sarah     | Hall     |  9    |
| 2     | Anna      | Watson   |  12   |
| 3     | Pep       | Boi      |  11   |



### How to Implement Tables in SQLite3

#### Creating Tables

Utilizing the ```CREATE TABLE``` command we can initialize a new table. In this example, we are creating the table above called Student into SQLite3. After the ```CREATE TABLE``` command we get to name our table. It's good practice to capitalize our table name.

```sql
CREATE TABLE Student(ID int, firstName text, lastName text, grade int);
```

#### Inserting Data into Tables

Utilizing the ```INSERT INTO``` command we can add data to our table. Following this command, we need to specify which table we would like to insert data into and then the use the```values``` command.  In this example, we are inserting four new students into our Student table that we created above. The data entered into the table must be in the order that we created the columns.



```sql
INSERT INTO Student values (0, "Jerad", "Hoy", 10);
INSERT INTO Student values (1, "Sarah", "Hall", 9);
INSERT INTO Student values (2, "Anna", "Watson", 12);
INSERT INTO Student values (3, "Pep", "Boi", 11);
```


#### How to Query the Data in SQLite
 
In order to see what tables we have in our database we use the ````.tables```` command shown below 

```sql 
.tables
```

This command will give us a list of all the tables in our database. In this example we will only retrieve our Student table shown below.

````Student````

To see the columns, entity types, and values of a select table we use the ````PRAGMA table_info```` command followed by the name of the table in parenthesis. 

```sql
PRAGMA table_info(Student);
``` 

To select all of the tuples or rows from a table we use the 
    ````SELECT * FROM````  command followed by the name of the table. For this tag we will need to add some additional information. In this example we're selecting every tuple from the Student table.
    
```sql
SELECT * FROM Student;
```

### Why SQLite should be integrated with Java

Often when designing a java application, you might run into the need to read in data from an external data source, such as a database running SQLite3.


### Structure of Java code

To connect a Java application to a SQL database, we will need to create several classes and methods to be able to model the data, read in from the database, and display or manipuate our data.

The Java code used to accomplish this can be partitioned into 2 sections:
+ Data access code
+ Entity classes

An entity is another name for a Java class that represents a table in our database. For example, our database has a table called Student, so our Java code should have an Entity called student.

### Creating Entities

For each table in our database, we need to create a Java class with the same name and information as the table. In our example, we will create a new class called Student with variables for each of the columns in our table as public variables. 

```java
public class Student
{
    public int ID;
    public String firstName;
    public String lastName;
    public int grade;
    
    public Student(
        int ID,
        String firstName,
        String lastName,
        int grade)
    {
        this.ID = ID;
        this.firstName = firstName;
        this.lastName = lastName;
        this.grade = grade;
    }
}
```
Make sure to include a constructor that initializes all variables in the class (as shown above). In this example we have ID, fistName, lastName, and grade.

### Creating the Data Access Code
In order to access data from your SQLite3 database, you will use 3 different classes that are provided by the Java SDK:
* java.sql.Connection
* java.sql.DriverManager
* java.sql.ResultSet

This section will discuss what these 3 classes do and how we use them to integrate Java code with our database.

#### java.sql.Connection
This class represents a connection to our SQLite database. We will use this connection to send a SQL query to our database via our code.

#### java.sql.DriverManager
The DriverManager defines the protocol for setting up a Connection to our database. We will use a DriverManager to create our aforementioned Connection.

#### java.sql.ResultSet
Think back to our previous example in which we executed the query ```SELECT * FROM Student```. This query returned 4 rows of data representing the 4 students in our database. ResultSet is a class which contains data returned by SQL queries; if we executed  ```SELECT * FROM Student``` programatically via our Connection, we'd get a ResultSet containing the 4 rows in our Student table.

Retrieving data from the ResultSet is a little tricky. There are 3 methods we will be using to achieve this:
* ResultSet.next()
* ResultSet.getInt(int columnIndex)
* ResultSet.getString(int columnIndex)

**ResultSet.next** moves to the next row of the returned data. Only call this method if we're done processing the data in that row.

**ResultSet.getInt** gets the value of the specified column in the current row. We only use this if we know the value is an int.

**ResultSet.getString** works the exact same way, but we use this when the value we want is a String.

Now that we've created our Student entity and understand the basics of the 3 classes used for interacting with SQLite3 databases, we can write some Java code to pull data from the database.

#### Retrieving Student Data
In order to connect to our database via Java code, we need to create a connection string. Connection strings are simply strings that define what driver we're using to connect, what type of database we're connecting to, and where the database files reside. Our connection string will be formatted like this:

> jdbc:sqlite:{DatabaseFilePath}\School.db

This connection string says we're using the JDBC driver and a SQLite database.

These are the steps we will follow to load in student data:
1. Define our connection String
2. Create our Connection
3. Execute a query to retrieve Student data via the Connection
4. Extract data from the query's ResultSet

##### Step 1: Define the Connection String
We use the following line of code to define the connection string according to the JDBC connection string format defined above. Your database file path may differ.

```java=
 String connectionString =
            "jdbc:sqlite:C:\\Users\\im5no\\Documents\\" +
            "NetBeansProjects\\SQLite3Integration\\src\\" +
            "sqlite3integration\\DatabaseFiles\\School.db";
```

##### Step 2: Create the Connection
Using the built in DriverManager and Connection classes described above, we set up a connection with our database with the following code:

```java=
Connection connection = DriverManager.getConnection(connectionString);
```

##### Step 3:  Execute a Query
First, define your query as a String.
```java=
String sqlQuery = "SELECT * FROM Student;";
```

Then execute the query via your Connection.
```java=
ResultSet studentResultSet =
	connection.createStatement().executeQuery(sqlQuery);
```

We now have access to the queried data via the returned ResultSet.

##### Create Student Objects with the Data
Create a list of students (our previously defined entity). We will instantiate students and add them to this list.
```java=
List<Student> allStudentsInDatabase = new ArrayList<>();
```

Iterate through all rows in our queried ResultSet with a while-loop using ResultSet.next. Inside of the while loop, create a new Student, using the values in the ResultSet's current row. These values are retrieved by using ResultSet.getInt and ResultSet.getString, and providing the column index (1-indexed) of that attribute.
```java=
while (studentResultSet.next())
{
	// The student's columns are defined in a previous section.
    Student nextStudent = new Student(
	    studentResultSet.getInt(1),		// The student's ID
        studentResultSet.getString(2),	// The student's first name
        studentResultSet.getString(3),	// The student's last name
        studentResultSet.getInt(4));	// The student's grade
    allStudentsInDatabase.add(nextStudent);
}
```

##### Using This Data
We now have a list of students to work with. Here's a simple demonstration of how to visualize the data we extracted from the database:

```java=
allStudentsInDatabase.forEach(student ->
            System.out.println(
                String.format(
                    "{ %1$s, %2$s, %3$s, %4$s }",
                    student.ID,
                    student.firstName,
                    student.lastName,
                    student.grade)));
```

Which results in:
```
{ 0, Jerad, Hoy, 10 }
{ 1, Sarah, Hall, 9 }
{ 2, Anna, Watson, 12 }
{ 3, Pep, Boi, 11 }
```


Integrating Java and SQLite is an easy and accessible process to those just learning to program and seasoned veterans alike. In no time you can successfully navigate and manipulate data using Java and SQLite. Query on! 
