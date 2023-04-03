
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
   0x08048755 <+14>:	sub    %eax,%ecx              %ecx = %ecx - %eax     result = 322424845 - pwd
   0x08048757 <+16>:	mov    %ecx,%eax
   0x08048759 <+18>:	mov    %eax,-0xc(%ebp)
   0x0804875c <+21>:	cmpl   $0x15,-0xc(%ebp)       (result > 21) ? jump test+259 : continue
   0x08048760 <+25>:	ja     0x804884a <test+259>
   
   0x0804884a <+259>:	call   0x8048520 <rand@plt>
   0x0804884f <+264>:	mov    %eax,(%esp)
   0x08048852 <+267>:	call   0x8048660 <decrypt>
   
   
   
}
```
if it is < 21, this result is used as input to decrypt() otherwise rand() picks a random value
 
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
the shl instruction does not modify the value stored at -0xc(%ebp)..


call decrypt(result)

```

 <decrypt+19>:	movl   $0x757c7d51,-0x1d(%ebp)
 <decrypt+26>:	movl   $0x67667360,-0x19(%ebp)
 <decrypt+33>:	movl   $0x7b66737e,-0x15(%ebp)
 <decrypt+40>:	movl   $0x33617c7d,-0x11(%ebp)

online Hex to Text:
517d7c75 + 60736667 + 7e73667b + 7d7c6133 = 517d7c75607366677e73667b7d7c6133 = "Q}|u`sfg~sf{}|a3"

s=$(echo -n "0x757c7d51" | xxd -r -p | rev) && s+=$(echo -n "0x67667360" | xxd -r -p | rev) && s+=$(echo -n "0x7b66737e" | xxd -r -p | rev) && s+=$(echo -n "0x33617c7d" | xxd -r -p | rev) && echo $s

python -c "print '757c7d51'.decode('hex')[::-1] + '67667360'.decode('hex')[::-1] + '7b66737e'.decode('hex')[::-1] + '33617c7d'.decode('hex')[::-1]"
  Q}|u`sfg~sf{}|a3
```

pwd = 0x1337d00d - input = result

stored at -0xc(%ebp)

"Q}|u`sfg~sf{}|a3" XOR each character with result
 
"Q}|u`sfg~sf{}|a3" XOR result = "Congratulations!"

The XOR operation is commutative
A XOR B = C <==> B = C XOR A = 

so suficiant to say:

```
level03@OverRide:~$ python -c 'print ord("Q") ^ ord("C")'
18
level03@OverRide:~$ python -c 'print ord("}") ^ ord("o")'
18
level03@OverRide:~$ python -c 'print ord("|") ^ ord("n")'
18

our pwd is
level03@OverRide:~$ python -c 'print '0x1337d00d' - 18'
322424827
```

```
level03@OverRide:~$ ./level03
Password:322424827
$ pwd
/home/users/level03
$ cat /home/users/level04/.pass
kgv3tkEb9h2mLkRsPkXRfc2mHbjMxQzvb2FrgKkf
```



