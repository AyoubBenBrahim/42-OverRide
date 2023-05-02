```c
int main()
{
    int number;

    puts("***********************************");
    puts("* \t     -Level00 -\t\t  *");
    puts("***********************************");
    printf("Password:");
    scanf("%d", &number);
    if(number != 5276)
    {
        puts("\nInvalid Password!");
        return(1);
    }
    puts("\nAuthenticated!");
    system("/bin/sh");
    return (0);
}
```