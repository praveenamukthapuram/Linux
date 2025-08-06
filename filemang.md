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
##2. Develop a C program to open an existing text file and display its contents.

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
