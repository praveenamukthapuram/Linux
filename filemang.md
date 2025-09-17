## 1. Write a c program to create a new text file and write "hi....' to it.
```c
#include <stdio.h>

int main()
{
        FILE *file;
        file=fopen("hello.txt","w");
        if(file==NULL)
        {
                printf("Error");
                return 1;
        }
        fprintf(file,"hiiiiiiiii\n");
        fclose(file);
        printf("Wrote Successfully\n");
        return 0;
}
```
## 2. Develop a C program to open an existing text file and display its contents.

```c
#include <stdio.h>

int main()
{
        FILE *file;
        char ch;

        file=fopen("hello.txt","r");

        if(file == NULL)
        {
                printf("Error");
                return 1;
        }

        while((ch = fgetc(file)) != EOF)
        {
                putchar(ch);
        }
        fclose(file);
        return 0;
}
```

## 3. What are the types of files present in Linux OS?

- Normal files = user data (text, binaries, etc.)
- Special files = system/IPC-related (dir, link, pipe, socket)
- Device files = represent hardware devices (char/block)

## 4. How do IPC Objects and how they are accessed?

- These are special files.
- IPC objects in linux are --> Pipes & Named Pipes
                           --> Message Queues
                           --> Shared Memory
                           --> Semaphores
                           --> Sockets
  
-   | IPC Object        | Creation Command / Call | Access/Use Call(s)            | Identifier      |
    | ----------------- | ----------------------- | ----------------------------- | --------------- |
    | Named Pipe (FIFO) | `mkfifo` (or `mknod`)   | `open()`, `read()`, `write()` | File path in FS |
    | Message Queue     | `msgget()`              | `msgsnd()`, `msgrcv()`        | Key (ftok)      |
    | Shared Memory     | `shmget()`              | `shmat()`, `shmdt()`          | Key (ftok)      |
    | Semaphore         | `semget()`              | `semop()`, `semctl()`         | Key (ftok)      |
    
- Named Pipes - A named pipe (FIFO) is a special file used for unidirectional communication between processes and can be accessed for unrelated process.
-              - Steps to create Named Pipes:
              1. Create a Named Pipe by using mkfifo/mknod.
                 i.e., mkfifo /tmp/myfifo -> Creates a special file in /tmp/myfifo.
              2. Open and Use in program.
                 i.e., int fd = open("/tmp/myfifo", O_WRONLY); // writer
                       write(fd, "Hello", 5);
                 i.e., int fd = open("/tmp/myfifo", O_RDONLY); // reader
                       char buf[100];
                       read(fd, buf, sizeof(buf));
              3. Shell Example
                 Terminal 1 (Writer) -> echo "Hello" > /tmp/myfifo
                 Terminal 2 (Reader) -> cat /tmp/myfifo
           
 - Message Queues - Managed by the kernel but not the filesystem and can be identified by a key using ftok().
  -                - Accessed by, msgget() → create/open a queue.
                                  msgsnd() → send message.
                                  msgrcv() → receive message.
               
                   - key_t key = ftok("progfile", 65);
                     int msgid = msgget(key, 0666 | IPC_CREAT);
                     msgsnd(msgid, &message, sizeof(message), 0);
                     msgrcv(msgid, &message, sizeof(message), 0, 0);

- Shared Memory - Fastest IPC and can be identified by a key like Message Queues.
-               - Create with shmget()
                - Attach with shmat()
                - Detach with shmdt()

                - key_t key = ftok("progfile", 65);
                  int msgid = msgget(key, 0666 | IPC_CREAT);
                  msgsnd(msgid, &message, sizeof(message), 0);
                  msgrcv(msgid, &message, sizeof(message), 0, 0);

- Semaphores - Used for synchronization.
-            - Create with semget()
             - Operated with semop()

- Socket - A socket is an endpoint for communication for two processes.
-        - It can work locally (same machine) or over a network (different machines).
         - It’s like a virtual plug where two programs connect to exchange data.
         - Identified by (IP address, Port) for network sockets or by a file path for Unix domain sockets.

         - Types , Stream socket (TCP) – Reliable, connection-oriented.
                   Datagram socket (UDP) – Faster, connectionless.
                   Unix domain socket – For local IPC.

## 5. User space application sends request to Hardware by using which I/O calls?
- In linux, a user space application cannot talk to hardware directly, it must go to kernel, using system calls that implement I/O.
- UserSpace -> KernelSpace -> Hardware
- User-space apps make I/O system calls → kernel drivers handle the hardware.
- The actual hardware access (registers, DMA, interrupts) is done only by the kernel/driver.
- Common system calls used,
-     System         Call Purpose
      open()	  Open a device file (e.g. /dev/ttyS0, /dev/sda).
      read()	  Read data from the device.
      write()	  Write data to the device.
      ioctl()	  Control/configure the device (special operations).
      mmap()	  Map device memory to user space (for faster I/O).
      close()	  Close the device file.
  
## 6. Why are basic I/O calls called universal I/O calls?
- Basic I/O calls are called universal I/O calls Low-Level I/O calls because the same set of system calls can perform I/O on any kind of file/device in Linux/Unix, providing a          single, consistent interface.
- open(), read(), write(), close(), lseek(), ioctl().

## 7. What is the content of the Inode Object?
- An inode (index node) is a data structure used by Unix/Linux filesystems to store metadata about a file.
- Every file has one inode, which the filesystem uses to locate and manage it.
- The inode does not store the file name or actual data; it stores information about the file.
- Contents of an inode objects,
-     Field in Inode	         Description
       File type	          Regular file, directory, symbolic link, etc.
       Permissions / Mode	  Read, write, execute bits for user, group, others (e.g., rwxr-xr-x).
       Owner ID (UID)	          User ID of the file owner.
       Group ID (GID)	          Group ID of the file’s group.
       File size	          Total size in bytes.
       Timestamps	          Access time (atime)

## 8. In which Object information of the file get stored?
- The inode object stores the information of a file.

## 9. Kernel uses which object to represent a file?
- Kernel uses inode object to represent a file.

## 10. Can we access the file information present in inode from the user application if yes, then how?
- We can access most of the file information stored in the inode from a user-space application in Linux.
- But we  can’t directly read the kernel’s inode structure (because it’s in kernel space), but Linux provides system calls and utilities that expose inode           information safely to user space.
- User Applications access Inode information by using , stat(), fstat(), lstat().
- By using stat(),

  ```c
     #include <stdio.h>
      #include <sys/stat.h>
      #include <unistd.h>

      int main() {
      struct stat fileStat;

      if (stat("myfile.txt", &fileStat) == -1) {
        perror("stat");
        return 1;
      }

      printf("Inode number: %ld\n", fileStat.st_ino);
      printf("File size: %ld bytes\n", fileStat.st_size);
      printf("Number of hard links: %ld\n", fileStat.st_nlink);
      printf("Owner UID: %d\n", fileStat.st_uid);
      printf("Group GID: %d\n", fileStat.st_gid);
      printf("Permissions: %o\n", fileStat.st_mode & 0777);

      return 0;
      }
  ```
- By using fstat(), (If your program already has file open
 
    ```c
    int fd = open("myfile.txt", O_RDONLY);
    struct stat st;
    fstat(fd, &st);
    printf("Inode number: %ld\n", st.st_ino);
  ```
    
- By using lstat(), (Command Line Utilities)

  ```c
   stat myfile.txt
   ls -i myfile.txt
  ```

## 11. Which system calls are used to access the file information?
- System calls to access the file information,
-     System Call	When to Use	                  What It Does
      stat()	        You have a filename	          Retrieves information (metadata) about a file specified by its path.
      lstat()	        You have a symbolic link	  Same as stat(), but if the file is a symbolic link, it returns information about the link itself instead                                                           of the target.
      fstat()	        You have a file descriptor        Retrieves file information for an already opened file (using its file descriptor).

  ```c
  #include <stdio.h>
  #include <sys/stat.h>
  #include <unistd.h>
  #include <fcntl.h>

  int main() {
    struct stat st;

    // Using stat()
    stat("myfile.txt", &st);
    printf("Inode: %ld\n", st.st_ino);

    // Using lstat() for symbolic links
    lstat("mylink", &st);
    printf("Link inode: %ld\n", st.st_ino);

    // Using fstat() for an open file
    int fd = open("myfile.txt", O_RDONLY);
    fstat(fd, &st);
    printf("Size: %ld bytes\n", st.st_size);
    close(fd);

    return 0;
  }
  ```

## 12. ls command internally invoked which system call to access the file information?
- Yes, ls internally uses,
-     getdents() (to read directory entries)
      stat() / lstat() (to access file information stored in the inode).

## 13. What happens after the kernel finds its inode object?
-     1. Kernel looks up the Inode object ->  Metadata of the file
      2. Kernel creates a File object     ->  open instance + offset + mode + link to inode
      3. Kernel returns File Descriptor   ->  Integer handle in your process.

-     User Space (Your Program)
      ─────────────────────────
      open("file.txt") ───────────────▶  Kernel Space

      Process File Descriptor Table
      ─────────────────────────────
      FD=3 ──────────────┐
                         │
                         ▼
            ┌────────────────────┐
            │  File Object       │  (struct file)
            │────────────────────│
            │ - Pointer to Inode │──┐
            │ - File Offset      │  │
            │ - Access Flags     │  │
            └────────────────────┘  │
                                    │
      Kernel VFS Layer
      ────────────────────────────────────────────────
                                    │
                                    ▼
                            ┌────────────────────┐
                            │  Inode Object      │  (struct inode)
                            │────────────────────│
                            │ - File Type        │
                            │ - Permissions      │
                            │ - Owner/Group      │
                            │ - Size             │
                            │ - Pointers to Data │───┐
                            │   Blocks on Disk   │   │
                            └────────────────────┘   │
                                                     │
      Disk (Filesystem Blocks)
      ─────────────────────────────────────────────────────
                                                     │
                                                     ▼
                                         Actual File Data Blocks

## 14. What are the contents of the file object?
- In Linux, file object corresponds to Kernel internal structure called struct file (defined in <linux/fs.h>).
- It is created when you open a file, and it represents open instance of that file.
- View of struct file,
  ```c
  struct file {
    struct inode    *f_inode;      // pointer to inode (file metadata)
    struct path     f_path;        // file path info
    unsigned int    f_flags;       // open flags
    mode_t          f_mode;        // access mode (R/W)
    loff_t          f_pos;         // current file offset
    struct file_operations *f_op;  // file operations
    void            *private_data; // driver-specific data
    struct address_space *f_mapping; // for memory-mapped files
   };
  ```

## 15. Kernel uses which object to represent the open files?
- The kernel uses the file object (struct file) to represent open files.

## 16. What is the difference between the primary data and other members of the file object?
- Kernel uses file object/struct file to represent an open file.
- It has two types of members, Primary Data (Per-Open Info) and Other Members (Shared/Stat Info).
-     Primary Data -> Holds Info that’s unique to this open instance of the file.
                   -> Example fields, f_pos (current offset), f_flags (open flags), f_mode (read/write mode), private_data (driver-specific data).
-     Other Members -> Pointers to shared information about the file.
                    -> f_inode (file metadata), f_op (file operations table), f_path (path info), f_mapping (mmap data).
```c
Process A FD → File Object A:
    f_pos = 0 (primary)
    f_flags = O_RDONLY (primary)
    f_inode → same inode (shared)
    f_op → same file operations (shared)

Process B FD → File Object B:
    f_pos = 100 (primary)
    f_flags = O_APPEND (primary)
    f_inode → same inode (shared)
    f_op → same file operations (shared)
```

## 17. Where does the file object base address get stored?
- The base address (pointer) of the file object is stored in the process’s file descriptor table (inside the kernel’s files_struct for that process).
-     Complete explanation,
      User Space (Process)
      ─────────────────────────────────────────────
      You call: fd = open("file.txt", O_RDONLY);

      You see:
      fd = 3  ← just an integer handle

      You DO NOT see:
      struct file * pointer (base address)
      ─────────────────────────────────────────────

      Kernel Space (Per-Process Structures)
      ─────────────────────────────────────────────
      task_struct (represents your process)
          
         └──> files_struct (open files info for process)
           │
           └──> fdtable[] (array of pointers)
                   ├─ Index 0 → struct file * (stdin)
                   ├─ Index 1 → struct file * (stdout)
                   ├─ Index 2 → struct file * (stderr)
                   ├─ Index 3 → struct file * (for "file.txt")
                   └─ ...
                          │
                          ▼
                 struct file (File Object in kernel memory)
                 ────────────────────────────────────────
                 Primary Data (per-open):
                   - f_pos (current offset)
                   - f_flags (open flags)
                   - private_data (driver-specific)
                 Other Members (shared/static):
                   - f_inode → struct inode *
                   - f_op → struct file_operations *
                   - f_path (dentry + mount info)
                   - f_mapping (address space)
                          │
                          ▼
                 struct inode (Inode Object in kernel memory)
                 ─────────────────────────────────────────
                   - File type (regular/dir/device)
                   - Permissions (mode bits)
                   - Owner UID/GID
                   - File size
                   - Timestamps (atime/mtime/ctime)
                   - Block pointers to actual data
                          │
                          ▼
                 Data Blocks on Disk (File Content)
      ─────────────────────────────────────────────

## 18. When the fd table is created and what is the size of the fd table?
- FD table is created at Process creation (fork()) and is populated when files are opened.
- The size of FD table is dynamic. At Process creation, it has a default (16, 32 and 256 entries per process, that depends on the Kernel version or config) and      increases dynamically when you open more files.
- The maximum FD count is controlled by,
-     The soft limit (check with ulimit -n) → often 1024 by default.
      The hard limit (can be increased by root).

      The kernel-wide max (in /proc/sys/fs/file-max).

## 19. When is the file object created?
- When we open a file.
-     User Process                          Kernel
      ────────────────────────────────────────────────────────────
      (1) open("file.txt", O_RDONLY)
      │
      ▼
      ────────────────────────────────────────────────────────────
      (2) Pathname Resolution
      - Kernel looks up directory entries
      - Finds the inode object of "file.txt"
      │
      ▼
      ────────────────────────────────────────────────────────────
      (3) File Object Allocation
      - Kernel allocates new struct file
      - Initializes:
        f_inode → inode object
        f_pos = 0
        f_flags = O_RDONLY
        f_op → file_operations
      │
      ▼
      ────────────────────────────────────────────────────────────
      (4) FD Table Entry Creation
      - Kernel finds first free slot in process’s fdtable[]
      - fdtable[fd] = pointer to new struct file
      │
      ▼
      ────────────────────────────────────────────────────────────
      (5) Return FD to User
      - User gets small int fd (e.g., 3)
      ────────────────────────────────────────────────────────────

## 20. What does open() returns?
- open()  -> >=0(i.e., non-negative) - Success
-         -> -1                      - Failure
```c
#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>

int main() {
    int fd = open("file.txt", O_RDONLY);

    if (fd == -1) {
        perror("open failed");
        return 1;
    }

    printf("open() returned fd = %d\n", fd);
    close(fd);
    return 0;
}
```

## 21. When a file opens for the first time by using open() calls in your program, what is returns?
- First open(), usually returns fd=3.
- Because, fd=0,1,2 represents (stdin,stdout,stderr).

## 22. Why do read() system calls need access to file objects?
- read() system call cannot access on raw file.
- It operates on FD, which maps to file objects.
- The file objects holds everything that Krenel needs to safely perform read() operation.
- Note: Same for write() system call also.

## 23. Which system call do we use to change the cursor position without reading and writing on a file?
- lseek() system call is used to change the cursor position without reading or writing on a file.
- off_t lseek(int fd, off_t offset, int whence);
-     fd      -> File descriptor of the open file
      offset -> Number of bytes to move the cursor
      whence -> Reference point for the offset:
                - SEEK_SET → from the beginning of the file
	        - SEEK_CUR → from the current position
	        - SEEK_END → from the end of the file
-     Returns, on success -> new file offset from the beginning of the file.
               -1         -> Error
  ```c
  #include <fcntl.h>
  #include <unistd.h>
  #include <stdio.h>

  int main() {
    int fd = open("file.txt", O_RDONLY);
    if(fd == -1) {
        perror("open");
        return 1;
    }

    // Move cursor 10 bytes from the beginning
    off_t pos = lseek(fd, 10, SEEK_SET);
    printf("Cursor moved to position: %ld\n", pos);

    close(fd);
    return 0;
  }
  ```

## 24. Can we open the same file from multiple processes? Explain the memory segment in kernel space and user space?
- Yes, we can open the same file from multiple processes.
- When each process calls open(),
-     1. Kernel finds the inode object.
      2. Allocates a new file object for that particular process.
  	  3. Stores a pointer to the file object in processes fd table.
- Example:
-     Process A opens file.txt → gets FD=3, file object A, inode X.
      Process B opens file.txt → gets FD=3 (in its own FD table), file object B, inode X.

- Memory Segments in User Space,
-     - Code Segment (text)
      - Data Segment (globals/statics)
      - Heap (dynamic allocations)
      - Stack (local variables)

- Memory Segments in Kernel Space,
-     - Text Segment:        Kernel code / system call instructions
      - Data Segment:        Global/static kernel variables
      - BSS Segment:         Uninitialized global/static variables
      - Heap:                Dynamically allocated objects
                             ├─ struct file (File Objects)
                             ├─ struct inode (Inodes)
                             └─ Buffers, caches, kmalloc allocations
      - Stack:               Per-process kernel stack
                             └─ Used during system calls, interrupts
      - Memory-Mapped I/O:   Hardware devices (I/O registers, framebuffers)

## 25. What is the difference between basic I/O calls and standard I/O calls?
- Basic I/O calls / Universal / Low-Level I/O calls:
-     - Direct system calls provided by the kernel to perform I/O
  	  - Operates on file descriptors (int)
  	  - No buffering; data goes directly to the kernel
      - open(), read(), write(), close(), lseek()
- Standard I/O calls / High-Level I/O calls:
-     - Library functions built on top of basic I/O calls, provided by the C standard library (stdio.h)
      - Operates on file pointers (FILE *)
      - Buffered in user space for efficiency
      - fopen(), fread(), fwrite(), fclose(), fseek()

## 26. Other than the basic I/O and standard I/O calls,Is there any method to access the file ?
-     Method	                            How It Works	                             Use Case
      Memory-Mapped I/O (mmap)	            Map file to memory, access like array	    Large files, shared memory
      Asynchronous I/O (aio_read/write)     Non-blocking read/write	                    High-performance I/O
      Network/Socket I/O	                Access remote files over network	        NFS, FTP, HTTP
      Device-Specific	                    Read/write special device files	            /dev devices
      High-Level Library APIs	            Language/framework abstractions	            C++, Python, DB libraries

## 27. How do user space applications get access to the content of inode objects?


  
