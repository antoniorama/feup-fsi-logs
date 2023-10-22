## Practical classes this week

- **Task 1:** Running both versions of the shellcode created a root shell that we could use.

- **Task 2:** Nothing to add, all we were asked to do was to compile the vulnerable program stack.c using the provided Makefile.

- **Task 3:** We were asked to perform and explain a buffer-overflow attack against a program called stack-L1. We first started by creating a file named `badfile` which would be read by the program. After that, using gdb on thr program `stack-L1-dbg`, we were able to visualize the `&buffer` and `$ebp` address values. [](../images/lb5i1.png)


## This week's CTF

- **Challenge 1:** After downloading and analyzing the given source code, we noticed that the program that was being run was using `scanf` to read from the user input and stored it in a `char buffer[32]`, which allowed us to perform a buffer overflow by inputting a string with more than 32 chars. Using a text to hex decoder, we found that `mem.txt => 0x6d656d2e7478740d0a`, and by using `gdb`, we set a breakpoint at the `scanf` function and ran the command `x/40xw &buffer`, which allowed us to view 40 bytes in memory starting at the `&buffer` memory address. After locating that the hex pattern for `mem.txt` was right after the 32nd byte in memory, we realized that we could input a string like `ABCABCABCABCABCABCABCABCABCABCABflag.txt\0` which consists of 32 chars and `flag.txt` right afterwards, which would lead to overwriting `mem.txt` in memory to `flag.txt`. After adding that string to the `r.sendline()` command in our `exploit_example.py` and setting the `DEBUG` value to `False`, the flag was revealed.

- **Challenge 2:** Much like the previous challenge, after analysing the code we realized that it was much the same, the `scanf` function was still being used and the `buffer` was still 32 chars in length. Only this time, a variable `char val[4]` was set to `0xdeadbeef`, and for the `fopen()` function to be ran, the value of `val` needed to be `0xfefc2324` in order to pass the `if` conditional. Following the same principle, we ran the command `x/44xw &buffer` to see 44 bytes in memory, where we then found the pattern of the `val` variable, which appeared in memory _before_ the `mem.txt` pattern. After some thought, and using the hex converter to generate the text value of `0xfefc2324`, which is `þü#$`, we created the input string `ABCABCABCABCABCABCABCABCABCABCAB$#üþflag.txt`, which consists of 32 chars, followed by overwriting the `val` variable with the decoded string written _backwards_ since the `checksec` program revealed it was a little endian system, and then overwriting the `file_name` variable with `flag.txt`, which, when ran using the `exploit_example.py` program after modifing the port to `4000`, revealed the flag.
