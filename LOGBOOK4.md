Task 1: export changes stuff, when printenv XDG_SESSION_CLASS = user, export XDG_SESSION_CLASS root, printenv XDG_SESSION_CLASS = root

Task 2: diff shows no difference, which means that env vars are inherited by the parent process

Task 3: without change prints nothing, with change prints current environment variables, because when 3arg is NULL, it passes no env vars, but when its environ, it passes the env vars

Task 4: It is verified, it passes the env vars

Task 5: After exporting env vars, the setuid child proc inherits them 

Task 6: yes i can make it run any arbitrary code, by putting it in the path of the system call, and it does run with root privileges

