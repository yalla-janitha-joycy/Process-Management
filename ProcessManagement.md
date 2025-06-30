# Process Management

## demonstrate the use of fork() 
c
#include<stdio.h>
#include<unistd.h>
#include<sys/types.h>
#include<sys/wait.h>

int main()
{
        int pid;
        printf("Before fork: Process ID is %d\n",getpid());

        pid=fork();

        if(pid < 0)
        {
                perror("fork failed");
                return 1;
        }
        else if(pid ==0)
        {
                printf("This is child process\n");
                printf("Child PID: %d\n",getpid());
                printf("Parent PID (from child): %d\n",getppid());
        }
        else
        {
                printf("This is the parent process\n");
                printf("Parent PID: %d\n",getpid());
                printf("Child PID (from parent) : %d\n",pid);
        }

        return 0;
}

## execvp() 

c
#include<stdio.h>
#include<unistd.h>
#include<sys/types.h>
#include<sys/wait.h>

int main()
{
        int pid;
        char *args[]={"ls","-l",NULL};

        pid=fork();
        if(pid < 0)
        {
                perror("fork failed");
                return 1;
        }

        if(pid == 0)
        {
                printf("Child process is running 'ls -l' using execvp()\n");
                execvp(args[0],args);
                perror("execvp failed");
                return 1;
        }
        else
        {
                wait(NULL);
                printf("Child process completed\n");
        }
        return 0;
}


## create a child process using fork() and print its PID.

c
#include<stdio.h>
#include<unistd.h>
#include<sys/wait.h>

int main()
{
        int pid;
        int status = 0;
        pid=fork();
        if(pid < 0)
        {
                perror("fork failed");
                return 1;
        }
        if(pid == 0 )
        {
                printf("This is child process\n");
                printf("Child PID : %d\n",getpid());
                printf("Parent PID (from child)) : %d\n",getppid());
                sleep(2);
        }
        else
        {
                printf("This is parent process\n");
                printf("Parent PID: %d\n",getpid());
                printf("Child PID(from parent) :%d\n",pid);
                wait(&status);
                printf("Child terminated with exit code : %d\n",WEXITSTATUS(status));
        }
        return 0;
}


##  create multiple child processes using fork() and display their PIDs.
c
#include<stdio.h>
#include<sys/wait.h>
#include<unistd.h>
#include<stdlib.h>
#define CHILDS 5
int main()
{
        int pid;

        for(int i=0;i<CHILDS;i++)
        {
                pid = fork();

                if(pid < 0)
                {
                        perror("fork failed");
                        exit(1);
                }
                else if(pid == 0)
                {
                        printf("Child %d: PID = %d,Parent PID =%d\n",i+1,getpid(),getppid());
                        return 0;
                }
        }

        for(int i=0;i < CHILDS ;i++)
        {
                wait(NULL);
        }
        return 0;
}


## create a zombie process 
c
#include<stdio.h>
#include<stdlib.h>
#include<sys/types.h>
#include<unistd.h>
#include<sys/wait.h>

int main()
{
        pid_t pid = fork();

        if(pid < 0)
        {
                perror("fork failed");
                return 1;
        }

        if(pid == 0)
        {
                printf("Child Process (PID: %d) is exiting\n",getpid());
                sleep(5);
                printf("Parent PID(from child) : %d\n",getppid());
        }
        else
        {
                printf("This is Parent proccess PID :%d\n",getpid());
                printf("Now it is ZOMBIE : Child PID : %d\n",pid);
        }
        return 0;
}

## waitpid().

c
#include<stdio.h>
#include<unistd.h>
#include<sys/wait.h>
#include<sys/types.h>
#include<stdlib.h>

int main()
{
        pid_t pid;
        pid = fork();
        if(pid < 0)
        {
                perror("fork failed");
                return 1;
        }
        if(pid == 0)
        {
                sleep(3);
                printf("\n[Child] PID :%d\n",getpid());
                printf("[Child] Parent PID:%d\n",getppid());
        }
        else
        {
                printf("[Parent] PID : %d\n",getpid());
                printf("[Parent] Created child with PID :%d\n",pid);
                printf("[Parent] Waiting for child to complete...\n");

                waitpid(pid,NULL,0);

                printf("\n[Parent] Child has completed.\n");
        }
        return 0;
}


##  create a daemon process. 

c
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<sys/types.h>
#include<sys/stat.h>
#include<fcntl.h>
#include<time.h>

int main()
{
        pid_t pid;
        printf("Running Process PID :%d\n",getpid());
        pid=fork();

        if(pid < 0)
        {
                perror("fork failed");
                exit(EXIT_FAILURE);
        }

        if(pid > 0)
        {
                printf("Daemon PID: %d\n",pid);
                exit(EXIT_SUCCESS);
        }

        if(setsid() < 0)
        {
                perror("setsid failed");
                exit(EXIT_FAILURE);
        }

        chdir("/");

        umask(0);

        close(STDIN_FILENO);
        close(STDOUT_FILENO);
        close(STDERR_FILENO);

        while(1)
        {
                int fd = open("/tmp/daemon_demo.log",O_WRONLY | O_CREAT | O_APPEND,0644);
                if( fd != -1)
                {
                        time_t now = time(NULL);
                        dprintf(fd,"Daemon active at : %s",ctime(&now));
                        close(fd);
                }
                sleep(5);
        }
        return 0;
}

##  demonstrate the use of the system() 

c
#include<stdio.h>
#include<unistd.h>
#include<sys/types.h>
#include<stdlib.h>

int main()
{
        printf("Executing 'ls -l' using system call\n");
        system("ls -l");
        printf("Creating new directory 'paradise' using system call\n");
        system("mkdir paradise");
        printf("System call execcuted successfully!\n");
        return 0;
}

##  create a process using fork() and pass arguments to the child process. 

c
#include<stdio.h>
#include<unistd.h>
#include<sys/wait.h>
#include<stdlib.h>

int main()
{
        int pid;
        pid=fork();

        if(pid < 0)
        {
                perror("fork failed");
                return 1;
        }

        if(pid == 0)
        {
                printf("Child Process PID :%d\n",getpid());
                printf("Executing 'ls -l using execlp()\n");

                execlp("ls","ls","-l",NULL);
                perror("execlp failed");
                exit(EXIT_FAILURE);
        }
        else
        {
                printf("Parent Process PID: %d\n",getpid());
                printf("Created Child with PID : %d\n",pid);
                wait(NULL);
                printf("Child process finished\n");
        }
        return 0;

}

## execvpe() 

c
#define _GNU_SOURCE
#include<unistd.h>
#include<stdio.h>
#include<stdlib.h>
#include<sys/types.h>
#include<sys/wait.h>


int main()
{
        int pid = fork();
        if(pid == 0)
        {
                printf("We're in child process\n");
                printf("Executing execvpe()\n");

                char *args[]={"ls","-l",NULL};
                char *envp[]={
                        "MYVAR=HelloWorld",
                        NULL
                };
                execvpe("ls",args,envp);
                perror("execvpe failed");
                return 1;
        }
        else if(pid > 0)
        {
                printf("This is parent process\n");
                printf("Waiting for the child to complete...\n");
                wait(NULL);
                printf("Child has completed.\n");
        }
        else
        {
                perror("fork failed");
                return 1;
        }
        return 0;
}

## create a process group and change its process group ID (PGID). 

c
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<sys/wait.h>
#include<sys/types.h>

int main()
{
        pid_t pid,pgid;
        pid=fork();

        if(pid < 0)
        {
                perror("fork failed");
                exit(EXIT_FAILURE);
        }

        if(pid == 0)
        {
                printf("\n[Child] Before changing PGID :\n");
                printf("PID = %d\n",getpid());
                printf("PGID = %d\n",getpgrp());

                if(setpgid(0,0) == -1)
                {
                        perror("setpgid failed in child");
                        exit(EXIT_FAILURE);
                }

                printf("[Child] After changing PGID : \n");
                printf("PID = %d\n",getpid());
                printf("PGID = %d\n",getpgrp());

                sleep(2);
                exit(EXIT_SUCCESS);
        }
        else
        {
                sleep(1);

                printf("\n[Parent]\n");
                printf("PID = %d\n",getpid());
                printf("Child PID : %d\n",pid);
                printf("Parent PGID = %d\n",getpgrp());

                pgid=getpgid(pid);
                if(pgid == -1)
                {
                        perror("getpgid failed in parent");
                }
                else
                {
                        printf("Child's PGID = %d\n",pgid);
                }
                wait(NULL);
        }
        return 0;
}


##  process synchronization using the fork() and wait() system calls. 
```c
#include<stdio.h>
#include<sys/wait.h>
#include<unistd.h>
#include<stdlib.h>


int main()
{
        int pid;
        pid=fork();
        if(pid < 0)
        {
                perror("fork");
                exit(1);
        }

        if(pid == 0)
        {
                printf("[Child] This is child process\n");
                sleep(2);
                printf("[Child] I got some tasks to do wait!\n");
                sleep(2);
                printf("[Child] Tasks are completed.\n");
        }
        else
        {
                printf("[Parent] This is Parent process\n");
                printf("[Parent] Waiting for child to finish\n");
                wait(NULL);
                printf("[Parent] Child has completed its tasks!\n");
        }
        return 0;
}
