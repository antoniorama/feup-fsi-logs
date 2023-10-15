## Practical classes this week

- **Task 1:** Running both versions of the shellcode created a root shell that we could use.
- **Task 2:** Nothing to add, all we were asked to do was to compile the vulnerable program stack.c using the provided Makefile.
- **Task 3:** 

## This week's CTF

- **Challenge 1:** After downloading and analyzing the given source code, we noticed that the program that was being run was using `scanf` to read from the user input and stored it in a `char buffer[32]`, which allowed us to perform a buffer overflow by inputting a string with more than 32 chars. Using a text to hex decoder, we found that `mem.txt => 0x6d656d2e7478740d0a`, and by using `gdb`, we set a breakpoint at the `scanf` function and ran the command `x/40xw &buffer`, which allowed us to view 40 of the byte values in memory starting at the `&buffer` memory address. After locating that the hex pattern for `mem.txt` was right after the 32nd byte in memory, we realized that we could input a string like `ABCABCABCABCABCABCABCABCABCABCABflag.txt\0` which consists of 32 chars and `flag.txt` right afterwards, which would lead to overwriting `mem.txt` in memory to `flag.txt`. After adding that string to the `r.sendline()` command in our `exploit_example.py` and setting the `DEBUG` value to `False`, the flag was revealed.


