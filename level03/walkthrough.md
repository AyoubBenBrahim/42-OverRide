
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
shl $0x2,%eax: Shift the bits of the value in the eax register to the left by 2 bits, 
                                             which is equivalent to multiplying by 4.
add $0x80489f0,%eax
mov (%eax),%eax: Move the value stored at the address in the eax register to the eax register.
jmp *%eax: Jump to the address stored in the eax register.
mov -0xc(%ebp),%eax
mov %eax,(%esp): Move the value in the eax register to the top of the stack.
call 0x8048660 <decrypt>: Call the function at address 0x8048660, which is decrypt.
```

```
 0x080488c1 <+103>:	call   0x8048530 <__isoc99_scanf@plt>
 0x080488c6 <+108>:	mov    0x1c(%esp),%eax
 0x080488ca <+112>:	movl   $0x1337d00d,0x4(%esp)
 0x080488d2 <+120>:	mov    %eax,(%esp)
 0x080488d5 <+123>:	call   0x8048747 <test>

pwd input stored at $esp+0x1c

test(pwd, 0x1337d00d)
{
   0x0804874d <+6>:	mov    0x8(%ebp),%eax         eax = pwd
   0x08048750 <+9>:	mov    0xc(%ebp),%edx         
   0x08048753 <+12>:	mov    %edx,%ecx              ecx = 322424845
   0x08048755 <+14>:	sub    %eax,%ecx              %ecx = %ecx - %eax              result = 322424845 - pwd
   0x08048757 <+16>:	mov    %ecx,%eax
   0x08048759 <+18>:	mov    %eax,-0xc(%ebp)
   0x0804875c <+21>:	cmpl   $0x15,-0xc(%ebp)       (result > 21) ? jump test+259 : continue
   0x08048760 <+25>:	ja     0x804884a <test+259>
   
   0x0804884a <+259>:	call   0x8048520 <rand@plt>
   0x0804884f <+264>:	mov    %eax,(%esp)
   0x08048852 <+267>:	call   0x8048660 <decrypt>
   
   
   
}
```

this result is used as input to decrypt() otherwise rand() picks a random value
