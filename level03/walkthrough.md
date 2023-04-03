
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
 

the value stored at -0xc(%ebp)..

```
 (gdb) p/d 0x1337d00d - 0x15 = 322424824

 run 
 
(gdb) ni
=> 0x8048773 <test+44>:	jmp    *%eax
   0x8048775 <test+46>:	mov    -0xc(%ebp),%eax
   0x8048778 <test+49>:	mov    %eax,(%esp)
   0x804877b <test+52>:	call   0x8048660 <decrypt>

(gdb) i r
eax            0x804883d	134514749
ecx            0x15	21

(gdb) x $eax
   0x804883d <test+246>:	mov    -0xc(%ebp),%eax
(gdb) ni
=> 0x804883d <test+246>:	mov    -0xc(%ebp),%eax
   0x8048840 <test+249>:	mov    %eax,(%esp)
   0x8048843 <test+252>:	call   0x8048660 <decrypt>
   0x8048848 <test+257>:	jmp    0x8048858 <test+273>
   0x804884a <test+259>:	call   0x8048520 <rand@plt>
   0x804884f <test+264>:	mov    %eax,(%esp)
   0x8048852 <test+267>:	call   0x8048660 <decrypt>
   0x8048858 <test+273>:	leave
   
(gdb) i r  $eax
eax            0x15	21
 ```
 
call decrypt(result)

```
xor    %eax,%eax   // sets the value of %eax to 0

 <decrypt+19>:	movl   $0x757c7d51,-0x1d(%ebp)
 <decrypt+26>:	movl   $0x67667360,-0x19(%ebp)
 <decrypt+33>:	movl   $0x7b66737e,-0x15(%ebp)
 <decrypt+40>:	movl   $0x33617c7d,-0x11(%ebp)

online Hex to Text:
517d7c75 + 60736667 + 7e73667b + 7d7c6133 = 517d7c75607366677e73667b7d7c6133 = "Q}|u`sfg~sf{}|a3"

s=$(echo -n "0x757c7d51" | xxd -r -p | rev) && s+=$(echo -n "0x67667360" | xxd -r -p | rev) && s+=$(echo -n "0x7b66737e" | xxd -r -p | rev) && s+=$(echo -n "0x33617c7d" | xxd -r -p | rev) && echo $s

python -c "print '757c7d51'.decode('hex')[::-1] + '67667360'.decode('hex')[::-1] + '7b66737e'.decode('hex')[::-1] + '33617c7d'.decode('hex')[::-1]"
  Q}|u`sfg~sf{}|a3
  
  
=> 0x80486c7 <decrypt+103>:	lea    -0x1d(%ebp),%eax  //  start of the string "Q}|u`sfg~sf{}|a3"
   0x80486ca <decrypt+106>:	add    -0x28(%ebp),%eax
   0x80486cd <decrypt+109>:	movzbl (%eax),%eax
   0x80486d0 <decrypt+112>:	mov    %eax,%edx
   0x80486d2 <decrypt+114>:	mov    0x8(%ebp),%eax   // x/wd $ebp+8 = 0x15 = 21
   0x80486d5 <decrypt+117>:	xor    %edx,%eax
   
   
   0x080486ed <decrypt+141>:	lea    -0x1d(%ebp),%eax           "Q}|u`sfg~sf{}|a3"
   0x080486f0 <decrypt+144>:	mov    %eax,%edx
   0x080486f2 <decrypt+146>:	mov    $0x80489c3,%eax            "Congratulations!"
   0x080486f7 <decrypt+151>:	mov    $0x11,%ecx                // loads the value 0x11 into %ecx, which will be used as the length of the strings to be compared.
   0x080486fc <decrypt+156>:	mov    %edx,%esi                 // %esi is often used as a pointer to a source string.  ("Congratulations!")
   0x080486fe <decrypt+158>:	mov    %eax,%edi                 // %edi is often used as a pointer to a destination string. ("Q}|u`sfg~sf{}|a3")
   0x08048700 <decrypt+160>:	repz cmpsb %es:(%edi),%ds:(%esi)
  
  
  
  test   %eax,%eax
bitwise AND operation between it and itself.
This is equivalent to checking whether the value in %eax is equal to 0.
```

result = 322424845 - pwd

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



