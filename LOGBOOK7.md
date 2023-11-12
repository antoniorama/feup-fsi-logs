### Practical classes this week

- **Task 1**: By using the included build_string.py script, without making any changes, we generated a `badfile` containing the built string. When we sent that badfile to the server using `cat badfile | nc 10.9.0.5 9090`, the program crashed.

![Alt image](images/lb7i3.png)

![Alt image](images/lb7i4.png)

- **Task 2a**: By trial and error changing the number of %x, we discovered that the minimum number of %x to get the server program to print out the first four bytes of our input is **64**.

![Alt image](images/lb7i1.png)

![Alt image](images/lb7i2.png)


- **Task 2b:** Using the %s format specifier and not providing an address, it tells the printf statement to read a string from an address present in the stack. In combination with the previous task's knowledge, we can force the %s format specifier to read a string from an address we input into the badfile. So by constructing the format string as the address of the secret variable (0x080b4008), followed by %x * 63 and a %s being the char in the 64th position, it will print out the content of the variable of the inputed address, in this case, the desired secret variable.

![Alt image](images/lb7i5.png)

![Alt image](images/lb7i6.png)
