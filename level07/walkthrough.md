```gdb
0x08048630  store_number
0x080486d7  read_number
0x08048723  main
```

it loads the env as argv
```
<main+22>:	mov    0x10(%ebp),%eax

(gdb) p **(char ***)($ebp+0x10)
$19 = 0xffffd8be "SHELL=/bin/bash"


(gdb) p **(char ***)($ebp+0xc)
$18 = 0xffffd8a2 "/home/users/level07/level07"

(gdb) x/10s 0xffffd8a2
0xffffd8a2:	 "/home/users/level07/level07"
0xffffd8be:	 "SHELL=/bin/bash"
0xffffd8ce:	 "TERM=xterm-256color"
0xffffd8e2:	 "SSH_CLIENT=10.12.10.15 49862 4242"
```

```
 0x08048878 <+341>:	lea    0x1b8(%esp),%eax
 0x08048882 <+351>:	call   0x80484a0 <fgets@plt>
   
 <main+413>:	mov    $0x8048d5b,%eax  "store"
 <main+427>:	repz cmpsb
 
 The test instruction is commonly used to check whether a value is zero or not
 0x080488df <+444>:	test   %eax,%eax
 0x080488e1 <+446>:	jne    0x80488f8 <main+469>         jump to compar if it is <+478>:	mov    $0x8048d61,%eax "read"
 0x080488e3 <+448>:	lea    0x24(%esp),%eax
 0x080488ea <+455>:	call   0x8048630 <store_number>
 
 <main+520>:	call   0x80486d7 <read_number>
   
```
 instruction `rep stos %eax,%es:(%edi)` is performing a "repeat string operation"
 
 that stores the value of the eax , ecx times at the memory location pointed to by edi.
 
 commonly used to initialize memory locations to a specific value

```
at <main+110>:	lea    0x24(%esp),%ebx
we insialize the buffer[0x64/100] to 0

this buffer is passed to store()

<main+448>:	lea    0x24(%esp),%eax
<main+455>:	call   0x8048630 <store_number>   store_number(&buffer)



<main+513>:	lea    0x24(%esp),%eax
<main+520>:	call   0x80486d7 <read_number>    read_number(&buffer)
```

debbugging


```

break at <main+351>:	call   0x80484a0 <fgets@plt>

Input command: store

store_number(array[100])
{

  <store_number+33>:	call   0x80485e7 <get_unum>
  <store_number+38>:	mov    %eax,-0x10(%ebp)
  
  get_unum()
  {
        <get_unum+26>:	mov    $0x8048ad0,%eax    "%u"
        <get_unum+31>:	lea    -0xc(%ebp),%edx
        <get_unum+41>:	call   0x8048500 <__isoc99_scanf@plt>      scanf("%u")
  }
  
  number at -0x10(%ebp)
  index  at -0xc(%ebp)
  
  <store_number+65>:	mov    $0xaaaaaaab,%edx  (a repeating pattern of 10101010101010101010101010101011 in binary / 2863311531) Convert each hex digit to 4 binary digits 
  
  <store_number+72>:	mul    %edx   ==> eax = eax * edx
  <store_number+74>:	shr    %edx  right-shifting the value of edx by one     edx = edx >> 1



}
```

Unsigned Divide by 3

'Assembly Optimization Tip


 a "magic number:" a number that lets you substitute speedy multiplication for pokey division, as if by magic.






Multiply to divide.

If you have a full 32-bit number and you need to divide, you can simply do a multiply and take the top 32-bit half as the result. This is faster because multiplication is faster than division.

Some C/C++ compilers do this automatically when optimization is turned on

If you want to divide by the odd number, and you know the number you want to divide is divisible without remainder, you can do this by multiplying by "magic" number and take the LOW half of the result, without need to do any shifts etc.

This approach is based on the theory of 2-adic numbers

https://web.archive.org/web/20210618232634/http://www.asmcommunity.net/forums/topic/?id=21308



the magic number 0xaaaaaaab

https://aakinshin.net/posts/perfex-div/


The principle of the magic number in method 3 is to implement the division operation of 32-bit divisor by multiplication. the compiler will multiply the divisor by a 32-bit reciprocal to form a 64-bit number, among them, the low 32-bit is the remainder, and the high 32-bit is the result we need. Below are some common magic numbers. For example, 0xaaaaaaab represents 2/3, and 0xcccccccd represents 4/5, you can search for related content on the Internet. 
https://topic.alibabacloud.com/a/unsigned-integer-division-using-shift-and-additionsubtraction-operations_8_8_31838351.html





2^32 / 3 = 0x55555555

Turns out this isn't very precise due to roundoff errors, so what people did was to increase the range to 33 bits and round up, and then right shift 'edx' by 1 (to divide by 2^33 instead of 2^32). For example (the +1 is due to rounding up the .(6) fraction):

2^33 / 3 + 1 = 0xAAAAAAAB

Code:
; eax = your number
mov edx, 0xAAAAAAAB
mul edx
shr edx, 1
; edx = output, eax/3    
https://board.flatassembler.net/topic.php?t=18975







if we multiply any integer N divisible by 3 by such infinite chain, we will get N/3. 

The first 32 bits of infinite 1/3 expansion are 0AAAAAAAB

https://board.flatassembler.net/topic.php?t=18975





The general method is:
1. Multiply n by a certain magic number.
2. Obtain the high-order half of the product and shift it right some
number of positions from 0 to 31.
3. Add 1 if n is negative.

the boaring math behind magic numbers
https://ridiculousfish.com/blog/posts/labor-of-division-episode-i.html



