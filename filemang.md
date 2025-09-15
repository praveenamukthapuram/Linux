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

## 4. How do IPC Objects ,named pipes, be accessed?
- These are special files.
- IPC objects in linux are --> Pipes & Named Pipes
                           --> Message Queues
                           --> Shared Memory
                           --> Semaphores
                           --> Sockets
  
