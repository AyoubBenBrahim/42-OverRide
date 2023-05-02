```c
int main()
{
    pid_t pid;
    char str[128];
    int number1; // esp+0xa8
    int number2; // esp+0x1c
    long ptrace_result;

    pid = fork();
    bzero(str, 128);
    number1 = 0;
    number2 = 0;

    if (pid == 0)
    {
        prctl(1, 1);
        ptrace(0, 0, 0, 0);
        puts("Give me some shellcode, k");
        gets(str);
    }
    else
    {
        while (1)
        {
            wait(&number2);
            printf("number2 = %d\n", number2);
            if ((number2 & 127) != 0)
            {
                if (((number2 & 127) + 1) >> 1 <= 0)
                {
                    number1 = ptrace(3, pid,(caddr_t) 44, 0);
                    if (number1 == 11)
                    {
                        puts("no exec() for you");
                        kill(pid, 9);
                        break;
                    }
                }
            }
            else
            {
                puts("child is exiting...");
                break;
            }
        }
    }
    return (0);
}
```