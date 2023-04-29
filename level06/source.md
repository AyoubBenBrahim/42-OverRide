
```c
int auth(char *login,int serial)
{
  size_t login_len;
  int ret;
  long lVar1;
  int i;
  uint hash;
  
  login_len = strcspn(login,"\n");
  login[login_len] = '\0';
  login_len = strnlen(login,0x20);
  if ((int)login_len < 6) 
  {
    ret = 1;
  }
  else 
  {
    lVar1 = ptrace(PTRACE_TRACEME);
    if (lVar1 == -1) 
    {
      puts("\x1b[32m.---------------------------.");
      puts("\x1b[31m| !! TAMPERING DETECTED !!  |");
      puts("\x1b[32m\'---------------------------\'");
      ret = 1;
    }
    else 
    {
      hash = ((int)login[3] ^ 0x1337U) + 0x5eeded;
      for (i = 0; i < (int)login_len; i = i + 1) 
      {
        if (login[i] < ' ') 
	{
          return 1;
        }
        hash = hash + ((int)login[i] ^ hash) % 0x539;
      }
      if (serial == hash)
        ret = 0;
      else
        ret = 1;
      
    }
  }
  return ret;
}
```

```c
int main(int argc, char **argv)
{

	unsigned int	serial;	
	char		login_buffer[32];

	puts("***********************************");
	puts("*\t\tlevel06\t\t  *");
	puts("***********************************");
	printf("-> Enter Login: ");
	fgets(login_buffer, 0x20, stdin);

	puts("***********************************");
	puts("***** NEW ACCOUNT DETECTED ********");
	puts("***********************************");
	printf("-> Enter Serial: ");
	scanf("%u", &serial);

	if(auth(login_buffer, serial) == 0)
	{
		puts("Authenticated!");
		system("/bin/sh");
		return (0);
	}
	// canary check
	return (1);
}
```
