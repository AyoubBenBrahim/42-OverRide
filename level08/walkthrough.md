```
0x00000000004008c4  log_wrapper
0x00000000004009f0  main
```

```
 <+90>:	mov    $0x400d6b,%edx           "w"
 <+95>:	mov    $0x400d6d,%eax           "./backups/.log"  
 <+106>:	callq  0x4007c0 <fopen@plt>   FILE *fopen(const char *path, const char *mode);
 <+111>:	mov    %rax,-0x88(%rbp)       FILE_PTR
```

```
<+161>:	mov    -0xa0(%rbp),%rax
<+168>:	add    $0x8,%rax

<+175>:	mov    -0x88(%rbp),%rax   FILE_PTR
<+182>:	mov    $0x400d96,%esi     "Starting back up: "
<+190>:	callq  0x4008c4 <log_wrapper>    log_wrapper(FILE_PTR)



```
