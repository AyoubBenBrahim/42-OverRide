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
 0x080488e1 <+446>:	jne    0x80488f8 <main+469>         jump to compar if it is 0 <+478>:	mov    $0x8048d61,%eax "read"
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
  
  Initialize two local variables with zeros.
  <+6>:     movl   $0x0,-0x10(%ebp)
  <+13>:    movl   $0x0,-0xc(%ebp)
  
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
  
  
  Perform a division-by-3 check using multiplication by the magic number 0xaaaaaaab.
  If the remainder is non-zero, skip the following block.
  
  0xaaaaaaab,%edx  (a repeating pattern of 10101010101010101010101010101011 in binary / 2863311531)
  Convert each hex digit to 4 binary digits
  check for more `[Ressources/magic_numbers.md](Ressources/magic_numbers.md)`
  
  <store_number+65>:	mov    $0xaaaaaaab,%edx 
  <store_number+72>:	mul    %edx   ==> eax = eax * edx
  <store_number+74>:	shr    %edx  right-shifting the value of edx by one     edx = edx >> 1

If the division-by-3 check passed, check if the most significant byte of the first local variable is equal to 0xb7. If not, skip the following block.

0x0804868a <+90>:    mov    -0x10(%ebp),%eax
0x0804868d <+93>:    shr    $0x18,%eax
0x08048690 <+96>:    cmp    $0xb7,%eax
0x08048695 <+101>:   jne    0x80486c2 <store_number+146>


If either of the checks failed, store the first local variable into an array pointed to by the first function argument, withan index specified by the second local variable. Set the return value to 0.

0x080486c2 <+146>:   mov    -0xc(%ebp),%eax
0x080486c5 <+149>:   shl    $0x2,%eax
0x080486c8 <+152>:   add    0x8(%ebp),%eax
0x080486cb <+155>:   mov    -0x10(%ebp),%edx
0x080486ce <+158>:   mov    %edx,(%eax)
0x080486d0 <+160>:   mov    $0x0,%eax


}
```
