```c
int store_number(uint *database_ptr)
{
  uint value;
  uint index;
  int ret;
  
  printf(" Number: ");
  value = get_unum();
  printf(" Index: ");
  index = get_unum();
  if ((index % 3 == 0) || (value >> 0x18 == 0xb7)) 
  {
    puts(" *** ERROR! ***");
    puts("   This index is reserved for wil!");
    puts(" *** ERROR! ***");
    ret = 1;
  }
  else 
  {
    *(database_ptr + (index * 4)) = value;  // database_ptr[index] = value
    ret = 0;
  }
  return ret;
}
```

```c
int read_number(unsigned int *database_ptr)
{
  int index;

  printf(" Index: ");
  index = get_unum();
  printf(" Number at data[%u] is %u\n", index, *(database_ptr + (index * 4)));
  return 0;
}
```

```c
int main(int argc, char ** argv, char **env)
{
	
	int database[100] = {0};
  char command[20] = {0};
	int ret = 0;
	char **ptr;

	ptr = argv;
	while (*ptr)
	{
		memset(*ptr, 0, strlen(*ptr));
	  ptr++;
	}
  
  ptr = env;
	while (*ptr)
	{
		memset(*ptr, 0, strlen(*ptr));
		ptr++;
	}

	puts(
		"----------------------------------------------------\n"
		"  Welcome to wil's crappy number database service!   \n"
		"----------------------------------------------------\n"
		" Commands:                                          \n"
		"    store - store a number into the data database    \n"
		"    read  - read a number from the data database     \n"
		"    quit  - exit the program                        \n"
		"----------------------------------------------------\n"
		"   wil has reserved some database :>                 \n"
		"----------------------------------------------------\n");
	
	while (true)
	{
		printf("Input command: ");

		fgets(command, sizeof(command), stdin);

		int len = strlen(command);
		command[len - 1] = '\0';
		
		int ret;

		if (strncmp(command, "store", 5) == 0)
		{
			ret = store_number(database);
		}
		else if (strncmp(command, "read", 4) == 0)
		{
			ret = read_number(database);
		}
		else if (strncmp(command, "quit", 4))
		{
			break;
		}

		if (ret)
		{
			printf(" Failed to do %s command\n", command);
		}
		else
		{
			printf(" Completed %s command successfully\n", command);
		}
		memset(command, 0, sizeof(command));
	}
	
}
```
