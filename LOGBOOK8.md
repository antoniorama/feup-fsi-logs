### SQL Injection Lab

- **Task 1**

The goal of this first task is to get familiar with SQL commands.
We started by connecting to this database on the MySQL container:

![Alt image](images/lb8i2.png)

After this, the goal was to use a SQL command to print all the profile information of the employee Alice.
We started by extracting all the information about this the **credential** table so we knew what the names of the columns were.
We did this with the command **SELECT * FROM credential;**.

![Alt image](images/lb8i3.png)

Now we know that the row we want to search from is called name, so we did this command to get all the information of the employee Alice:
**SELECT * FROM credential WHERE name = 'Alice';**

![Alt image](images/lb8i4.png)

- **Task 2.1**

The goal of this task is logging in the admin account, knowing that the usename is admin.

We have access to the authentication code implementation of the website:

![Alt image](images/lb8i5.png)

We see that they don't use prepare statements, so we can do SQL Injection with the following input:

![Alt image](images/lb8i6.png)

This input chooses username admin, closes the bracket, ends the statement and comments out the rest of the querry.

When we log in, we see that we have access to all the employee's details:

![Alt image](images/lb8i7.png)

- **Task 2.2**

The goal of this task was to do the request in the terminal using curl. We encoded the special characters used in the username field and got the following command:

** curl 'http://www.seed-server.com/unsafe_home.php?username=admin'%3B%23&Password=' ** 

The response contained the table with all employee's information:

![Alt image](images/lb8i8.png)
