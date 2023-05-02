```c

void decrypt(int number)
{
    int index;
    int len;
    char str[17] = "Q}|u`sfg~sf{}|a3";
    int result;

    len = strlen(str);
    index = 0;
    while (index < len)
    {
        result = number ^ str[index];
        str[index] = (char)result & 255; // the bitwise AND operator & is used to extract the least significant byte of the result variable.
        index++;
    }
    if (!strncmp(str, "Congratulations!", 17))
        system("/bin/sh");
    else
        puts("\nInvalid Password");
}

void test(int password, int number)
{
    int result = number - password;

    if (result > 21)
        decrypt(rand());
    else
        decrypt(result);
}

int main()
{
    int password;

    srand(time(NULL));
    puts("***********************************");
    puts("*\t\tlevel03\t\t**");
    puts("***********************************");
    printf("Password:");
    scanf("%d", &password);
    test(password, 322424845);
    return (0);
}
```