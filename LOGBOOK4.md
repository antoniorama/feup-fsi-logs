Task 1: When using `export`, we can change certain environment variables or add our own, for example if we run `printenv`, we can see that it prints to the terminal `XDG_SESSION_CLASS=user`, but if we run `export XDG_SESSION_CLASS=hello` and then check with `printenv`, it prints to the terminal `XDG_SESSION_CLASS=hello`.

Task 2: After routing both versions of the file with the different commented lines to different files `file1` and `file2`, when we use the `diff` command it shows no difference between the files. We can reach the conclusion then that environment variables are passed from the parent process to the child process.

Task 3: In the original version of the code, it prints nothing to the terminal, but after changing the 3rd argument of `execve` to the external char `environ`, it now prints the current environment variables. This is because, if we check the `man execve` page, we see that the third argument of the function is the environment variables pointers array, and by passing `NULL` to it in the beginning, it will not print anything when it runs the `env` command.

Task 4: We came to the conclusion that yes, the `system` function does indeed pass on the current environment variables to the function call.

Task 5: We came to the conclusion that the `Set-UID` child process does indeed inherit the environment variables we set before running the shell program using the `export` command.

Task 6: We reached the conclusion that yes, by specifing a different path in the system call or by changing the `PATH` environment variable before running the process and creating an executable with an identical name to the one that's supposed to be called, we can run any arbitrary code file with the `Set-UID` program. After verifying, yes the `Set-UID` program running our malicious code file **does** run with `root` priviledges.

