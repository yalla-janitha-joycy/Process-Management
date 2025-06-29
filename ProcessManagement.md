# Process Management

## Write a C program to demonstrate the use of fork() system call. 
```c
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
```
## Write a C program to illustrate the use of the execvp() function. 
```c
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

```
## Write a program in C to create a child process using fork() and print its PID.
```c
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

```
##  Write a C program to create multiple child processes using fork() and display their 
PIDs.
```c
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

```
## Write a program in C to create a zombie process ?
```c
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
```
## Write a C program to demonstrate the use of the waitpid() function for process synchronization. 
```c
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

```
##  Write a program in C to create a daemon process. 
```c
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
```
##  Write a C program to demonstrate the use of the system() function for executing shell commands. 
```c
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
```
##  Write a C program to create a process using fork() and pass arguments to the child process. 
```c
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
```
##  Write a program in C to demonstrate process synchronization using semaphores.
```c
#include<stdio.h>
#include<unistd.h>
#include<sys/types.h>
#include<sys/sem.h>
#include<sys/ipc.h>
#include<sys/wait.h>

union sem
{
        int val;
};

int main()
{
        key_t key=ftok("semfile",65);
        int semid=semget(key,1, 0666 |IPC_CREAT);

        union sem u;
        u.val = 1;
        semctl(semid,0,SETVAL,u);

        pid_t pid = fork();

        struct sembuf p ={0,-1,0};
        struct sembuf v ={0,1,0};

        if(pid == 0)
        {
                semop(semid, &p,1);
                printf("Child in critical section\n");
                sleep(2);
                printf("Child leaving critical section\n");
                semop(semid,&v,1);
        }
        else
        {
                semop(semid,&p,1);
                printf("Parent in critical section\n");
                sleep(2);
                printf("Parent leaving critical section\n");
                semop(semid,&v,1);
                wait(NULL);
                semctl(semid,0,IPC_RMID);
        }
        return 0;
}
```
## Write a C program to demonstrate the use of the execvpe() function. 
```c
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
```
## Write a C program to create a process group and change its process group ID (PGID). 
```c
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
```
##  Write a program in C to demonstrate inter-process communication (IPC) using shared memory. 
```c
#include<stdio.h>
#include<unistd.h>
#include<stdlib.h>
#include<sys/ipc.h>
#include<sys/shm.h>
#include<string.h>
#include<sys/types.h>
#include<sys/wait.h>

#define SHM_SIZE 1024

int main()
{
        int key;
        int shmid;
        char *shm_ptr;

        key=ftok("/tmp",65);

        if(key == -1)
        {
                perror("frok");
                exit(EXIT_FAILURE);
        }

        shmid=shmget(key,SHM_SIZE,0666 | IPC_CREAT);
        if(shmid < 0)
        {
                perror("shmget");
                exit(EXIT_FAILURE);
        }

        pid_t pid = fork();

        if(pid < 0)
        {
                perror("fork failed");
                exit(EXIT_FAILURE);
        }

        if(pid == 0)
        {
                sleep(1);

                shm_ptr=(char *)shmat(shmid,NULL,0);
                if(shm_ptr == (char *)(-1))
                {
                        perror("shmat in child");
                        exit(EXIT_FAILURE);
                }
                printf("[Child] Data read from shared memory: \"%s\"\n",shm_ptr);

                shmdt(shm_ptr);
                exit(EXIT_SUCCESS);
        }
        else
        {
                shm_ptr = (char *)shmat(shmid,NULL,0);
                if(shm_ptr == (char *)(-1))
                {
                        perror("shmat in parent");
                        exit(EXIT_FAILURE);
                }
                const char *msg = "Hey!,my Child";
                strncpy(shm_ptr,msg,SHM_SIZE);
                printf("[Parent] Data written to shared memory: \"%s\"\n",msg);

                wait(NULL);

                shmdt(shm_ptr);
                shmctl(shmid,IPC_RMID,NULL);
        }

        return 0;
}
```
## Write a C program to create a child process using vfork() and demonstrate its usage. 
```c
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<sys/types.h>

int global = 10;
int main()
{
        pid_t pid;
        int local = 5;

        pid= vfork();

        if(pid < 0)
        {
                perror("vfork");
                exit(EXIT_FAILURE);
        }

        if(pid == 0)
        {
                printf("[Child] PID = %d, PPID = %d\n",getpid(),getppid());

                global = global + global;
                local = local + local;

                printf("[Child] Modified global = %d, local = %d\n",global,local);
                _exit(0);
        }
        else
        {
                printf("[Parent] PID = %d\n",getpid());
                printf("[Parent] global = %d,local = %d\n",global,local);
        }
        return 0;
}
```
##  Write a C program to create a pipeline between two processes using the pipe() system call.
```c
#include<stdio.h>
#include<unistd.h>
#include<stdlib.h>
#include<string.h>
#include<sys/types.h>


int main()
{
        int pipefd[2];

        int pid;
        char buffer[100];

        if(pipe(pipefd) == -1)
        {
                perror("pipe");
                exit(EXIT_FAILURE);
        }

        pid = fork();

        if(pid < 0)
        {
                perror("fork");
                exit(EXIT_FAILURE);
        }

        if (pid == 0)
        {
                close(pipefd[1]);

                read(pipefd[0],buffer,sizeof(buffer));
                printf("[Child] Received message : \"%s\"\n",buffer);

                close(pipefd[1]);
        }
        else
        {
                close(pipefd[0]);

                const char *data= "Hello! How are you child?";
                write(pipefd[1],data,strlen(data)+1);
                printf("[Parent] Sent message through pipe\n");
                close(pipefd[1]);
                exit(EXIT_SUCCESS);
        }
        return 0;
}
```
##  Write a C program to demonstrate the use of the clone() system call to create a thread.
```c
#define _GNU_SOURCE

#include<stdio.h>
#include<stdlib.h>
#include<sched.h>
#include<unistd.h>
#include<sys/wait.h>

#define STACK_SIZE 1024 * 1024

int thread(void *arg)
{
        printf("Child thread : %s\n",(char*)arg);
        return 0;
}
int main()
{
        char *stack=malloc(STACK_SIZE);
        if(!stack)
        {
                perror("malloc");
                exit(1);
        }

        char *message = "Hello from clone!";
        int clone_flags=SIGCHLD;

        pid_t pid=clone(thread,stack+STACK_SIZE,clone_flags,message);
        if(pid == -1)
        {
                perror("clone");
                free(stack);
                exit(1);
        }
        waitpid(pid,NULL,0);
        printf("Main: child thread has exited\n");

        free(stack);
        return 0;
}
```
## Write a C program to create a child process using fork() and communicate between parent and child using pipes. 
```c
#include<stdio.h>
#include<fcntl.h>
#include<unistd.h>
#include<stdlib.h>
#include<string.h>
#include<sys/wait.h>

int main()
{
        int fds[2];
        int pid;
        char buffer[100];
        if(pipe(fds) == -1)
        {
                perror("pipe");
                exit(1);
        }
        pid=fork();
        if(pid < 0)
        {
                perror("fork");
                exit(1);
        }

        if(pid == 0)
        {
                close(fds[1]);
                read(fds[0],buffer,sizeof(buffer));
                printf("Child received : %s\n",buffer);
                close(fds[0]);
        }
        else
        {
                close(fds[0]);
                char *msg="Pipe : Hey! Child";
                write(fds[1],msg,strlen(msg)+1);
                close(fds[1]);
                wait(NULL);
                printf("Child has read\n");
        }
        return 0;
}
```
##  Write a C program to demonstrate process synchronization using the fork() and wait() system calls. 
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
```
## Write a C program to create a child process using fork() and demonstrate inter-process communication (IPC) using shared memory.
```c
#include<stdio.h>
#include<stdlib.h>
#include<sys/ipc.h>
#include<sys/shm.h>
#include<sys/types.h>
#include<sys/wait.h>
#include<string.h>
#include<unistd.h>

#define KEY 200211
#define SHM_SIZE 1024

int main()
{
        int shmid;
        char *data;

        shmid=shmget(KEY,SHM_SIZE,0666 | IPC_CREAT);
        if(shmid < 0)
        {
                perror("shmget");
                exit(1);
        }

        pid_t pid = fork();
        if(pid < 0)
        {
                perror("fork");
                exit(1);
        }
        else if(pid == 0)
        {
                data = (char*)shmat(shmid,NULL,0);
                if(data == (char*)-1)
                {
                        perror("shmat");
                        exit(1);
                }
                printf("[Child] Reading from shared memory : \"%s\"\n",data);
                shmdt(data);
        }
        else
        {
                data= (char*)shmat(shmid,NULL,0);
                if(data == (char*)-1)
                {
                        perror("shmat");
                        exit(1);
                }

                strcpy(data, "Hello from parent via shared memory!");
                wait(NULL);
                shmdt(data);
                shmctl(shmid,IPC_RMID,NULL);
        }
        return 0;
}
```
## Write a C program to create a child process using fork() and demonstrate process synchronization using semaphores.
```c
#include<stdio.h>
#include<sys/sem.h>
#include<sys/wait.h>
#include<sys/ipc.h>
#include<unistd.h>
#include<stdlib.h>

#define SEM_KEY 200211
#define SEM_SIZE 1024

union semun
{
        int val;
};

int main()
{
        int semid = semget(SEM_KEY, SEM_SIZE,0666 | IPC_CREAT);

        union semun sem;
        sem.val = 0;
        semctl(semid,0,SETVAL,sem);
        struct sembuf p = {0,-1,0};
        struct sembuf v = {0,1,0};

        pid_t pid = fork();
        if(pid == 0)
        {
                printf("[Child] Doing some tasks..\n");
                sleep(2);
                printf("[Child] Signaling parent..\n");
                semop(semid,&v,1);
        }
        else
        {
                printf("[Parent] Waiting for child..\n");
                semop(semid,&p,1);
                printf("[Parent] Received signal from child\n");
                wait(NULL);
                semctl(semid,0,IPC_RMID);
        }
        return 0;
}
```
## Write a C program to create a child process using fork() and demonstrate process communication using message queues. 
```c
#include<stdio.h>
#include<stdlib.h>
#include<sys/ipc.h>
#include<sys/msg.h>
#include<unistd.h>
#include<string.h>
#include<sys/wait.h>

#define KEY 200211
#define SIZE 1024

struct message
{
        long m_type;
        char data[SIZE];
};

int main()
{
        int msgid;
        struct message msg;
        msgid = msgget(KEY,0666 | IPC_CREAT);
        if(msgid == -1)
        {
                perror("msgget");
                exit(1);
        }

        pid_t pid = fork();
        if(pid < 0)
        {
                perror("fork");
                exit(1);
        }
        else if(pid == 0)
        {
                msg.m_type = 1;
                strcpy(msg.data,"Hello from child process!");
                msgsnd(msgid,&msg,sizeof(msg.data),0);
                printf("[Child] Message sent to parent\n");
        }
        else
        {
                wait(NULL);
                msgrcv(msgid,&msg,sizeof(msg.data),1,0);
                printf("[Parent] Received message: \"%s\"\n",msg.data);
                msgctl(msgid,IPC_RMID,NULL);
        }
        return 0;
}
```
## Write a C program to create a child process using fork() and demonstrate process communication using sockets. 
```c
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<sys/socket.h>
#include<string.h>
#include<errno.h>

#define SIZE 1024

int main()
{
        int sv[2];
        pid_t pid;
        char buffer[SIZE];

        if(socketpair(AF_UNIX,SOCK_STREAM,0,sv) == -1)
        {
                perror("socketpair");
                exit(EXIT_FAILURE);
        }

        pid =fork();
        if(pid < 0)
        {
                perror("fork");
                exit(EXIT_FAILURE);
        }

        if(pid == 0)
        {
                close(sv[0]);
                const char *child_msg = "Hello from child!";
                send(sv[1],child_msg,strlen(child_msg) + 1,0);
                recv(sv[1],buffer,SIZE,0);
                printf("Child received: %s\n",buffer);
                close(sv[1]);
        }
        else
        {
                close(sv[1]);
                recv(sv[0],buffer,SIZE,0);
                printf("Parent received : %s\n",buffer);
                const char *parent_msg = "Hello from parent!";
                send(sv[0],parent_msg,strlen(parent_msg) + 1,0);
                close(sv[0]);
        }
        return 0;
}
```
## Write a C program to create a child process using fork() and demonstrate process synchronization using mutexes. 
```c
#include<stdio.h>
#include<stdlib.h>
#include<pthread.h>
#include<unistd.h>
#include<sys/mman.h>
#include<sys/wait.h>

int main()
{
        pthread_mutex_t *mutex = mmap(NULL,sizeof(pthread_mutex_t),PROT_READ|PROT_WRITE, MAP_SHARED|MAP_ANONYMOUS, -1,0);

        pthread_mutexattr_t attr;
        pthread_mutexattr_init(&attr);
        pthread_mutexattr_setpshared(&attr,PTHREAD_PROCESS_SHARED);

        pthread_mutex_init(mutex,&attr);

        pid_t pid = fork();

        if(pid < 0)
        {
                perror("fork");
                exit(EXIT_FAILURE);
        }
        if(pid == 0)
        {
                pthread_mutex_lock(mutex);
                printf("[Child] This is Child.\n");
                pthread_mutex_unlock(mutex);
        }
        else
        {
                wait(NULL);
                pthread_mutex_lock(mutex);
                printf("[Parent] This is Parent.\n");
                pthread_mutex_unlock(mutex);


                pthread_mutex_destroy(mutex);
                pthread_mutexattr_destroy(&attr);

                munmap(mutex,sizeof(pthread_mutex_t));
        }
        return 0;
}
```
## Write a C program to create a child process using fork() and demonstrate process communication using named pipes (FIFOs). 
```c
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<fcntl.h>
#include<sys/stat.h>
#include<sys/types.h>
#include<string.h>
#include<sys/wait.h>

#define FIFO "/tmp/my_fifo"
#define SIZE 100
int main()
{

        unlink(FIFO);
        if(mkfifo(FIFO,0666) == -1)
        {
                perror("mkfifo");
                exit(EXIT_FAILURE);
        }

        pid_t pid = fork();
        if(pid < 0)
        {
                perror("fork failed");
                exit(EXIT_FAILURE);
        }
        else if(pid == 0)
        {
                char buffer[SIZE];
                int fd = open(FIFO,O_RDONLY);
                if(fd == -1)
                {
                        perror("Child : open FIFO for reading");
                        exit(EXIT_FAILURE);
                }

                int n = read(fd,buffer,SIZE-1);
                if(n > 0)
                {
                        buffer[n] = '\0';
                        printf("Child received : %s\n",buffer);
                }
                else
                {
                        perror("Child : Read");
                }
                close(fd);
                exit(0);
        }
        else
        {
                const char *msg = "Hello from parent process!";
                int fd = open(FIFO,O_WRONLY);
                if(fd == -1)
                {
                        perror("Parent: open FIFO for writing");
                        exit(EXIT_FAILURE);
                }
                write(fd,msg,strlen(msg));
                close(fd);

                wait(NULL);

                unlink(FIFO);# Process Management

## demonstrate the use of fork() 
```c
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
```
## execvp() 

```c
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

```
## create a child process using fork() and print its PID.

```c
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

```
##  create multiple child processes using fork() and display their PIDs.
```c
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

```
## create a zombie process 
```c
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
```
## waitpid().

```c
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

```
##  create a daemon process. 

```c
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
```
##  demonstrate the use of the system() 

```c
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
```
##  create a process using fork() and pass arguments to the child process. 

```c
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
```
## demonstrate process synchronization using semaphores.
```c
#include<stdio.h>
#include<unistd.h>
#include<sys/types.h>
#include<sys/sem.h>
#include<sys/ipc.h>
#include<sys/wait.h>

union sem
{
        int val;
};

int main()
{
        key_t key=ftok("semfile",65);
        int semid=semget(key,1, 0666 |IPC_CREAT);

        union sem u;
        u.val = 1;
        semctl(semid,0,SETVAL,u);

        pid_t pid = fork();

        struct sembuf p ={0,-1,0};
        struct sembuf v ={0,1,0};

        if(pid == 0)
        {
                semop(semid, &p,1);
                printf("Child in critical section\n");
                sleep(2);
                printf("Child leaving critical section\n");
                semop(semid,&v,1);
        }
        else
        {
                semop(semid,&p,1);
                printf("Parent in critical section\n");
                sleep(2);
                printf("Parent leaving critical section\n");
                semop(semid,&v,1);
                wait(NULL);
                semctl(semid,0,IPC_RMID);
        }
        return 0;
}
```
## execvpe() 

```c
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
```
## create a process group and change its process group ID (PGID). 

```c
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
```

##  demonstrate process synchronization using the fork() and wait() system calls. 
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

        }
        return 0;
}
```
