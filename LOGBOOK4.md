## Practical class of this week

Task 1: When using `export`, we can change certain environment variables or add our own, for example if we run `printenv`, we can see that it prints to the terminal `XDG_SESSION_CLASS=user`, but if we run `export XDG_SESSION_CLASS=hello` and then check with `printenv`, it prints to the terminal `XDG_SESSION_CLASS=hello`.

Task 2: After routing both versions of the file with the different commented lines to different files `file1` and `file2`, when we use the `diff` command it shows no difference between the files. We can reach the conclusion then that environment variables are passed from the parent process to the child process.

Task 3: In the original version of the code, it prints nothing to the terminal, but after changing the 3rd argument of `execve` to the external char `environ`, it now prints the current environment variables. This is because, if we check the `man execve` page, we see that the third argument of the function is the environment variables pointers array, and by passing `NULL` to it in the beginning, it will not print anything when it runs the `env` command.

Task 4: We came to the conclusion that yes, the `system` function does indeed pass on the current environment variables to the function call.

Task 5: We came to the conclusion that the `Set-UID` child process does indeed inherit the environment variables we set before running the shell program using the `export` command.

Task 6: We reached the conclusion that yes, by specifing a different path in the system call or by changing the `PATH` environment variable before running the process and creating an executable with an identical name to the one that's supposed to be called, we can run any arbitrary code file with the `Set-UID` program. After verifying, yes the `Set-UID` program running our malicious code file **does** run with `root` priviledges.


## This week's CTF

After connecting to the server using the `netcat` command, we were immediatly greeted at the home folder of a user named `flag_reader`.
Reading the `admin_note.txt` allowed us to understand that we could use the `/tmp` directory in order to create and write files, since almost the entire rest of the system was in read only mode for our connection. Using the command `ls -la` on the home folder gave us some pretty good insight on some of the files, the most important one being that the `/home/flag_reader/env` file was just a shortcut for the `/tmp/env` file.

Using the `cat` command on `my_script.sh`, we could see that it would run the `reader` file located in that same folder, but before that it would source the environment variables through the previously mentioned `env` folder. Using the same command on the `main.c` file on which the `reader` executable was compiled from, we saw that it used the `access()` function from the C standard library to verify that the file `/flags/flag.txt` existed.

From this point, what we did was `cd` into the `/tmp` directory and compiled a snippet of C code as a library to override the `access()` function being called by the script. The code used was:
```c
#include <stdio.h>
#include <unistd.h>

int access(const char *pathname, int mode){
    system("cat /flags/flag.txt >> /tmp/new.txt");
    return 0;
}
```

This code file, when compiled to a library, overrides the `access()` function and runs a system function that calls to the shell to use the `cat` command in order to write the contents of the `/flags/flag.txt` file into a readable file created by us `/tmp/new.txt`.

After using the `echo` command to write to the `env` file inside `/tmp` the line `LD_PRELOAD=/tmp/lib.so`, which forces our compiled fake library to preload when the program compiles, overriding the normal `access` function, we just had to wait for the server to run the script `my_script.sh` periodically and eventually the flag was written in our specified `/tmp/new.txt` file.

