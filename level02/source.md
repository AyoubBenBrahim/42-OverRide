```c
int main(int ac, char **av)
{
    char content[41];
    char username[100];
    char password[100];
    int index;
    size_t size;
    FILE *file;

    bzero(content, 41);
    bzero(username, 100);
    bzero(password, 100);
    file = fopen("/home/users/level03/.pass", "r");
    if (file == 0)
    {
        fwrite("ERROR: failed to open password file\n", 1, 36, stderr);
        exit(1);
    }
    size = fread(content, 1, 41, file);
    index = strcspn(content, "\n");
    content[index] = 0;
    if (size != 41)
    {
        fwrite("ERROR: failed to read password file\n", 1, 36, stderr);
        fwrite("ERROR: failed to read password file\n", 1, 36, stderr);
        exit(1);
    }
    fclose(file);
    puts("===== [ Secure Access System v1.0 ] =====");
    puts("/***************************************\\");
    puts("| You must login to access this system. |");
    puts("\\**************************************/");
    printf("--[ Username: ");
    fgets(username, 100, stdin);
    index = strcspn(username, "\n");
    username[index] = 0;
    printf("--[ Password: ");
    fgets(password, 100, stdin);
    index = strcspn(password, "\n");
    password[index] = 0;
    puts("*****************************************");
    if (strncmp(content, password, 41) != 0)
    {
        printf(username);
        puts(" does not have access!");
        exit(1);
    }
    printf("Greetings, %s!\n", username);
    system("/bin/sh");
    return (0);
}
```