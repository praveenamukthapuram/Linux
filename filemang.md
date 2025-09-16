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
  
