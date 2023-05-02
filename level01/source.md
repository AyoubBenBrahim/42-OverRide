```c
char a_user_name[256];

int verify_user_name(void)
{
    puts("verifying username....\n");
    return (strncmp(a_user_name, "dat_wil", 7));
}

int verify_user_pass(char *str)
{
    return strncmp(str, "admin", 5);
}

int main(int ac, char **av)
{
    char str[64];
    bzero(str, 64);
    puts("********* ADMIN LOGIN PROMPT *********");
    printf("Enter Username: ");
    fgets(a_user_name, 256, stdin);

    if (verify_user_name() != 0)
    {
        puts("nope, incorrect username...\n");
        return (1);
    }
    puts("Enter Password: ");
    fgets(str, 100, stdin);
    if (verify_user_pass(str) == 0)
    {
        puts("nope, incorrect password...\n");
        return (1);
    }
    puts("nope, incorrect password...\n");
    return (0);
}
```