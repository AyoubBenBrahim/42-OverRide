```
0x00000000004008c4  log_wrapper
0x00000000004009f0  main
```

```
 <+90>:	mov    $0x400d6b,%edx           "w"
 <+95>:	mov    $0x400d6d,%eax           "./backups/.log"  
 <+106>:	callq  0x4007c0 <fopen@plt>   FILE_PTR *fopen(const char *path, const char *mode);
 <+111>:	mov    %rax,-0x88(%rbp)       FILE_PTR
 
  <+118>:	cmpq   $0x0,-0x88(%rbp)      cmp(0, FILE_PTR)
  <+126>:	jne    0x400a91 <main+161>
  <+128>:	mov    $0x400d7c,%eax        "ERROR: Failed to open %s\n"
  <+133>:	mov    $0x400d6d,%esi        "./backups/.log"
  <+138>:	mov    %rax,%rdi
  <+141>:	mov    $0x0,%eax
  <+146>:	callq  0x400730 <printf@plt>
  <+151>:	mov    $0x1,%edi
  <+156>:	callq  0x4007d0 <exit@plt>
 
```

```
<+161>:	mov    -0xa0(%rbp),%rax         argv
<+168>:	add    $0x8,%rax

<+175>:	mov    -0x88(%rbp),%rax         FILE_PTR
<+182>:	mov    $0x400d96,%esi           "Starting back up: "
<+190>:	callq  0x4008c4 <log_wrapper>   log_wrapper(FILE_PTR)

log_wrapper()
{
    <+47>:	mov    -0x120(%rbp),%rdx
    <+54>:	lea    -0x110(%rbp),%rax
    <+61>:	mov    %rdx,%rsi
    <+64>:	mov    %rax,%rdi
    <+67>:	callq  0x4006f0 <strcpy@plt>   strcpy( -0x110(%rbp) , -0x120(%rbp) )
}


```
