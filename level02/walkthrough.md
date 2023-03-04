
```
level02@OverRide:~$ ./level02
===== [ Secure Access System v1.0 ] =====
/***************************************\
| You must login to access this system. |
\**************************************/
--[ Username:
```
```
<+245>:	lea    -0xa0(%rbp),%rax ==> result of reading "/home/users/level03/.pass" = the Flag
<+252>:	mov    $0x400bf5,%esi   ==> "\n"
<+257>:	mov    %rax,%rdi
<+260>:	callq  0x4006d0 <strcspn@plt>
```
```
strcspn() calculates the length of the number of characters before the 1st occurrence of character present in both the string
Parameters:
str1 : The Target string in which search has to be made.
str2 : Argument string containing characters to match in target string.
```
strcspn("PwBLgNa8p8MTKW57S7zxVAQCxnCpV8JqTTs9XEBv", "\n"). // the flag from level01

In this case, there is no occurrence of the newline character \n in the first string, so the function will return the length of the entire string, which is 40.
```
 <+260>:	callq  0x4006d0 <strcspn@plt>
 <+265>:	movb   $0x0,-0xa0(%rbp,%rax,1)
 <+273>:	cmpl   $0x29,-0xc(%rbp)       ===> ret fread() = -0xc(%rbp)
INTEL 
 <+265>:	mov    BYTE PTR [rbp+rax*1-0xa0],0x0
 <+273>:	cmp    DWORD PTR [rbp-0xc],0x29
```
```
rbp+rax*1-0xa0 = 0 ==> rbp-0xa0 + rax ==> *(rbp-0xa0 + rax) = 0 ==> buffer + rax ==> buffer[rax] = 0 
then compares the res of fread() to 41
```
```
if not equal; then 
  0x400bf8:	 "ERROR: failed to read password file\n"
  exit(1)
else jmp to <+361>

fclose(*File) // -0x8(%rbp)
```
```
the program really begins at <+373>
0x400c20:	 "===== [ Secure Access System v1.0 ] ====="
```
```
 <+431>:	mov    0x20087e(%rip),%rax        # 0x601248 <stdin@@GLIBC_2.2.5>
 <+438>:	mov    %rax,%rdx
 <+441>:	lea    -0x70(%rbp),%rax
 <+445>:	mov    $0x64,%esi
 <+450>:	mov    %rax,%rdi
 <+453>:	callq  0x4006f0 <fgets@plt>
```
```
<+453>: fgets Username from stdin and stores it at -0x70(%rbp)
<+523>:	fgets Username from stdin and stores it at -0x110(%rbp)

<+566>:	lea    -0x110(%rbp),%rcx ==> the PWD
<+573>:	lea    -0xa0(%rbp),%rax  ==> The Flag

<+591>:	callq  0x400670 <strncmp@plt>

strncmp(Flag, PWD, 0x29/41);

if jne    <main+642>
   printf(-0x70(%rbp) ) // username
   "username" does not have access!
   exit
else
  printf("Greetings, %s!\n", -0x70(%rbp)) // username
  system (/bin/sh)
```

