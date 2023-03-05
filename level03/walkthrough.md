
```
ssh level03@10.12.100.228 -p 4242
Hh74RPnuQ9sa5JAEXgNWCqz7sXGnh5J5M9KfPg3H
```
```
(gdb) info func

0x08048617  get_unum
0x0804864f  prog_timeout
0x08048660  decrypt
0x08048747  test
0x0804885a  main

```


p 0x1337d00d - input = output

test <+21>:	cmpl   $0x15,-0xc(%ebp) // output

jump if above

checking if the value at ebp-0xc is greater than 0x15, and if so, it jumps 


```
   0x0804875c <+21>:	cmpl   $0x15,-0xc(%ebp)
   0x08048760 <+25>:	ja     0x804884a <test+259>
   0x08048766 <+31>:	mov    -0xc(%ebp),%eax
   0x08048769 <+34>:	shl    $0x2,%eax
   0x0804876c <+37>:	add    $0x80489f0,%eax
   0x08048771 <+42>:	mov    (%eax),%eax
   0x08048773 <+44>:	jmp    *%eax
   0x08048775 <+46>:	mov    -0xc(%ebp),%eax
   0x08048778 <+49>:	mov    %eax,(%esp)
   0x0804877b <+52>:	call   0x8048660 <decrypt>
```

```
shl $0x2,%eax: Shift the bits of the value in the eax register to the left by 2 bits, which is equivalent to multiplying by 4.
add $0x80489f0,%eax
mov (%eax),%eax: Move the value stored at the address in the eax register to the eax register.
jmp *%eax: Jump to the address stored in the eax register.
mov -0xc(%ebp),%eax
mov %eax,(%esp): Move the value in the eax register to the top of the stack.
call 0x8048660 <decrypt>: Call the function at address 0x8048660, which is decrypt.
```



