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
- Basic I/O calls are called universal I/O calls because the same set of system calls can perform I/O on any kind of file/device in Linux/Unix, providing a          single, consistent interface.
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


  
