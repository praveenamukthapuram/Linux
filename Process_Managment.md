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

  
