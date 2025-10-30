## 1. Explain the concept of process creation in operating systems.
- A Process is a running instance of a program.
- It has,
-     Its own memory space (code, data, stack, heap)
-     Registers and Program Counters
-     Open files and resources
-     A unique Process ID(PID).
- When you run a program (like ls), OS creates a Process.
  
- Process Creation,
-     1. Parent Process Initiates -> An existing process (often called the parent) requests the OS to create a new process.
                                  -> This is done using system calls (e.g., fork() in Unix/Linux, CreateProcess() in Windows).
      2. Allocate Resources       -> A PID for the new process
                                  -> Memory (copy or share of parent’s memory)
                                  -> File descriptors
                                  -> CPU scheduling data structures
      3. Child Process Creation   -> A new process called the child is created.
                                  -> The child initially inherits attributes from the parent:
                                      -> Open files
                                      -> Working directory
                                      -> Environment variables
                                      -> User credentials
      4. Load Program (Optional)  -> The child process can replace its memory image with a new program using exec() (Linux/Unix).
                                  -> This lets it run a completely different program from the parent.
      5. Execution Begins         -> The new process starts executing (either as a copy of the parent or as a new program after exec()).
      6. Process Termination      ->  When finished, the process calls exit() to terminate.
                                  -> The OS frees its resources.
                                  -> The parent can read the exit status using wait().
  
- System Calls Involved (Linux/Unix)
-     System     Call	Purpose
      fork()	   Create a new process (child) by duplicating the parent.
      exec()     family (execl, execv, etc.)	Load a new program into the child’s memory.
      wait()	   Parent waits for child process to finish.
      exit()	   Child terminates and returns status to parent.

- Process Creation Tree,
-     The first process is typically init (or systemd in Linux).
-     All other processes are descendants of this initial process.
-     This forms a process hierarchy (tree).

- Example in Linux (C Code)
  ```c
  #include <stdio.h>
  #include <unistd.h>

  int main() {
    pid_t pid = fork();   // create child process

    if (pid == 0) {
        // Child process
        printf("Child process: PID = %d\n", getpid());
    } else {
        // Parent process
        printf("Parent process: PID = %d, Child PID = %d\n", getpid(), pid);
    }

    return 0;
  }
  ```

## 2. Differentiate between the fork() and exec() system calls.
-     Feature	             fork()	                                                             exec()
      Definition	         Creates a new child process by duplicating the parent process.	     Replaces the current process image with a new program.
      Purpose	             To create a new process.	                                           To execute a new program in the current process.
      Process Creation	   Yes – creates a child process.	                                     No – does not create a new process.
      Execution Flow	     Both parent and child continue executing after fork().	             The current process is replaced; the old code stops running.
      Return Value	       Returns 0 to the child, and child PID to the parent.	               Returns only if it fails (returns -1).
      Memory Sharing	     Child gets a copy of parent’s memory (copy-on-write).	             Entire memory of current process is replaced by the new program.
      PID (Process ID)	   Child has a unique PID different from parent.	                     Same PID is retained since no new process is created.
      Program Image	       Remains the same for both parent and child.                         Replaced by the new program.
      Used For	           Creating concurrent processes. 	                                   Running a new executable file.
      Typical Use Case	   Used before exec() to create a child process.	                     Used after fork() to run another program in the child.
      Returns Twice?	     Yes — once in parent, once in child.	                               No — only returns if execution fails.
      Example Function	   pid_t pid = fork();	                                               execlp("ls", "ls", "-l", NULL);

##3. Write a C program to demonstrate the use of fork() system call.
```c
#include <stdio.h>
#include <unistd.h>

int main() {
    pid_t pid;

    printf("Before fork()\n");

    pid = fork();  // Create a new process

    if (pid < 0) {
        // fork() failed
        printf("Fork failed!\n");
        return 1;
    }
    else if (pid == 0) {
        // Child process
        printf("This is the Child Process.\n");
        printf("Child PID: %d\n", getpid());
        printf("Parent PID (from child): %d\n", getppid());
    }
    else {
        // Parent process
        printf("This is the Parent Process.\n");
        printf("Parent PID: %d\n", getpid());
        printf("Child PID (from parent): %d\n", pid);
    }

    printf("This line is executed by both parent and child.\n");
    return 0;
}
```

##4. Difference between execvp() and execlp()?
-     execvp() - Executes a new program, with arguments passed as an array (vector of strings).
               - int execvp(const char *file, char *const argv[]);
-     execlp() - Executes a new program, with arguments passed as a list (explicitly, one by one).
               - int execlp(const char *file, const char *arg, ..., (char *)NULL);

##5. Write a C program to illustrate the use of the execvp() function.
```c
#include <stdio.h>
#include <unistd.h>

int main() {
    // Define command and its arguments (must end with NULL)
    char *args[] = {"ls", "-l", NULL};

    printf("Before execvp()\n");

    // Replace current process image with the "ls -l" command
    execvp("ls", args);

    // This line runs only if execvp() fails
    perror("execvp failed");

    return 0;
}
```

##6. How does the vfork() system call differ from fork()?
-     fork()	   Creates a new child process that is an exact copy of the parent process, with its own address space.
      vfork()	   Creates a new child process, but shares the parent’s address space temporarily until the child either calls exec() or _exit().

##7. Discuss the significance of the getpid() and getppid() system calls.
-     getpid()	 Returns the process ID (PID) of the calling process.
      getppid()	 Returns the parent process ID (PPID) of the calling process.

##8. Explain the concept of process termination in UNIX-like operating systems.
-     When a process finishes its task or encounters an error, it stops running and releases all resources it was using (like memory, file descriptors, etc.) is called Process Termination.
-     It can can be done by,
        - Normal Termination         -> The process completes its task successfully and calls the exit() system call.
        - Error Termination          -> The process detects an error and calls exit() with a non-zero status.
        - Abnormal Termination       -> The process is terminated by another process or by the kernel using a signal (e.g., SIGKILL, SIGTERM).
        - Killed by another Process  -> The parent or another process sends a termination signal (using kill() system call).
        -  Killed by kernel           -> The kernel terminates a process due to illegal operations (like segmentation fault, division by zero).
-     System calls involved,
        - exit(status)        -> Called by the process itself to terminate and return an exit status to the parent.
        - _exit(status)       -> Lower-level version used inside the system (no cleanup of stdio buffers).
        - wait() / waitpid()  -> Called by the parent process to collect the child’s termination status and prevent zombie processes.
-     During Termination Process,
        - All open files are closed.
        - Memory (heap, stack, data segments) is released.
        - Child processes may be assigned to the init (or systemd) process if the parent terminates first.
        - The process moves into a zombie state temporarily until the parent reads its exit status using wait() or waitpid().
        - Once the parent collects the status, the process is completely removed from the process table.
-     Zombie process   -> A terminated process whose exit status has not yet been collected by its parent. It still occupies an entry in the process table.
-     Orphan Process   -> A child process whose parent has already terminated. The init process (PID 1) adopts it.

##9. Write a program in C to create a child process using fork() and print its PID.
```c
#include <stdio.h>
#include <unistd.h>   // for fork(), getpid(), getppid()
#include <sys/types.h>

int main() {
    pid_t pid;

    // Create a child process
    pid = fork();

    if (pid < 0) {
        // Error case
        printf("Fork failed!\n");
        return 1;
    }
    else if (pid == 0) {
        // This block executes in the child process
        printf("Child Process:\n");
        printf("Child PID: %d\n", getpid());
        printf("Parent PID: %d\n", getppid());
    }
    else {
        // This block executes in the parent process
        printf("Parent Process:\n");
        printf("Parent PID: %d\n", getpid());
        printf("Child PID: %d\n", pid);
    }

    return 0;
}
```

##10. Describe the process hierarchy in UNIX-like operating systems.
- The process hierarchy in UNIX-like systems is a tree structure starting from the init (PID 1) process. Every process is created by another process using fork(), may replace itself with a new program using exec(), and terminates using exit(). Parent–child relationships define the structure and management of all        running processes.

##11. What is the purpose of the exit() function in C programming?
- The exit() function in C is used to terminate a program (process) in a controlled and clean manner. It ensures that all resources are properly released before the program ends.

##12. Explain how the execve() system call works and provide a code example.
- The execve() system call in UNIX-like operating systems is used to replace the current running process with a new program.

##13. Discuss the role of the fork() system call in implementing multitasking.
- It allows a process to create a copy of itself, enabling multiple processes to run concurrently — which is the foundation of multitasking.
- Multitasking -> Multitasking is the ability of an operating system to execute multiple processes seemingly at the same time by sharing CPU time among them.
-     Each process:
        - Has its own memory space, program counter, stack, and resources.
        - Can execute independently.
- Role of fork() in Multitasking,
        - The fork() system call is used to create a new process (called a child process) from an existing one (the parent process).
        - After fork(), both processes run concurrently, each with its own copy of memory and execution flow.
- fork() enables multitasking by,
- Process Duplication        --> When a process calls fork(), the operating system creates a duplicate of that process.
                             --> The new process (child) gets its own memory space, program counter, and registers.
- Parallel Execution         --> Both the parent and child processes start executing independently after the fork() call.
                             --> The OS scheduler allocates CPU time slices to each process, allowing concurrent execution.
- Independent Tasks          --> The parent and child can perform different tasks.
                             --> For example, the parent may handle input while the child performs computation or I/O operations.
- Basis for Process Trees    -->Using multiple fork() calls, a process can create multiple children, forming a process hierarchy — the backbone of multitasking.

##14, 



  
