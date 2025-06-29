#  Process Management in Linux – System Programming with C

This repository contains C programs demonstrating essential *process management concepts in Linux*, using system calls such as fork(), exec(), wait(), getpid(), and exit(). These programs help you understand how Linux handles process creation, execution, termination, and inter-process relationships.

---

##  Table of Contents

- [ What is Process Management?](#-what-is-process-management)
- [ Process Concepts Covered](#-process-concepts-covered)
- [ System Calls Used](#-system-calls-used)
- [ Folder Structure](#-folder-structure)
- [ How to Compile and Run](#-how-to-compile-and-run)
- [ Sample Use Cases](#-sample-use-cases)
- [ Author](#-author)

---

##  What is Process Management?

*Process Management* is a key function of any operating system that deals with the creation, scheduling, and termination of processes. A process is an instance of a program in execution.

In Linux, system calls are provided to allow C programs to control and interact with these processes directly.

---

##  Process Concepts Covered

This repo includes C programs demonstrating:

-  fork() – Creating a new process
-  exec() – Replacing a process’s memory space with a new program
-  wait() / waitpid() – Waiting for child process termination
-  getpid() / getppid() – Getting process and parent process IDs
-  exit() – Terminating a process
-  Parent-child relationship
-  Zombie & orphan process creation
-  Process synchronization basics

---

##  System Calls Used

| System Call   | Purpose                                   |
| ------------- | ----------------------------------------- |
| fork()      | Creates a new (child) process              |
| execvp()    | Replaces current process image             |
| wait()      | Parent waits until child finishes          |
| getpid()    | Returns current process ID                 |
| getppid()   | Returns parent process ID                  |
| exit()      | Ends a process and returns exit status     |

---