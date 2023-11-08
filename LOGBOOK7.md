### Practical classes this week

- **Task 1**: By using the included build_string.py script, without making any changes, we generated a `badfile` containing the built string. When we sent that badfile to the server using `cat badfile | nc 10.9.0.5 9090`, the program crashed.

![Alt image](images/lb7i3.png)

![Alt image](images/lb7i4.png)

- **Task 2**: By trial and error changing the number of %x, we discovered that the minimum number of %x to get the server program to print out the first four bytes of our input is **64**.

![Alt image](images/lb7i1.png)

![Alt image](images/lb7i2.png)
