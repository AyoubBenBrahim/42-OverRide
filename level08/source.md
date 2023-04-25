```c
void log_wrapper(FILE *log_PTR, char *log_msg, char *fileName) {
    char current_char;
    size_t buff_len, fileName_length;
    ulong i, j;
    long saved_frame_pointer;
    char buffer[264];

    saved_frame_pointer = *(long *)(in_FS_OFFSET + 40);
  
    strcpy(buffer, log_msg);
    buff_len = strlen(buffer);

    fileName_length = strlen(fileName);
    snprintf(buffer + buff_len, 255 - buff_len, "%s", fileName);
    buffer[255] = '\0';
    fileName_length = strcspn(buffer, "\n");
    buffer[fileName_length] = '\0';
    fprintf(log_PTR, "LOG: %s\n", buffer);

    if (saved_frame_pointer != *(long *)(in_FS_OFFSET + 40)) {
        __stack_chk_fail();
    }
}
```

```c
int main(int argc, char **argv)
{
  FILE  *log_PTR;
  FILE  *input_file_PTR;
  char  *file = "./backups/";
  int   fd;
  char  c;

  if (argc != 2)
    printf("Usage: %s filename\n",*argv,*argv);

  log_PTR = fopen("./backups/.log","w");
  if (log_PTR == NULL)
  {
    printf("ERROR: Failed to open %s\n", "./backups/.log");
    exit(1);
  }

  log_wrapper(log_PTR, "Starting back up: ", argv[1]);

  input_file_PTR = fopen(argv[1],"r");
  if (input_file_PTR == NULL)
  {
    printf("ERROR: Failed to open %s\n", argv[1]);
    exit(1);
  }

  strncat(file, argv[1], strlen(argv[1]));
  fd = open(file, 0xc1, 0x1b0);
  if (fd < 0) {
    printf("ERROR: Failed to open %s%s\n", "./backups/", argv[1]);
    exit(1);
  }
  while(c = fgetc(input_file_PTR))
    write(fd, &c, 1);

  log_wrapper(log_PTR, "Finished back up ", argv[1]);

  fclose(input_file_PTR);
  close(fd);
  return 0;
}
```
