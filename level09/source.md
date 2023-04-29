```c
void main(void)
{
  puts(
      "--------------------------------------------\n|   ~Welcome to l33t-m$n ~    v1337        |\n- -------------------------------------------"
      );
  handle_msg();
  return 0;
}
```

```c
void handle_msg(void)
{
  struct* struct_address // 140;
  
  memset(struct_address, 0, sizeof(struct_address));

  set_username(&struct_address);
  set_msg(&struct_address);
  puts(">: Msg sent!");
  return;
}
```

```c
void set_username(long struct_address)
{
  char buffer[17];
  int i;

  puts(">: Enter your username");
  printf(">>: ");

  fgets(buffer, 0x80, stdin);

  // Copy username from buffer to user data structure
  for (i = 0; (i < 41 && (buffer[i] != '\0')); i = i + 1) 
  {
    *(char *)(struct_address + 140 + (long) i) = buffer[i];
  }

  // Print welcome message with username
  printf(">: Welcome, %s", (char *)(struct_address + 140));
  return;
}
```
```c
void set_msg(char *struct_address)
{
  char *buffer_pointer;
  char buffer[128];
  int msg_len;

  puts(">: Msg @Unix-Dude");
  printf(">>: ");

  // Read message from user input into buffer
  fgets(buffer, 0x400, stdin);

  // Copy message from buffer to msg_buffer
  msg_len = *(int *)(msg_buffer + 0xb4);
  strncpy(struct_address, buffer, msg_len);

  return;
}
```
```c
void secret_backdoor(void) {
  char cmd[128];

  fgets(cmd, 128, stdin);
  system(cmd);

  return;
}
```
