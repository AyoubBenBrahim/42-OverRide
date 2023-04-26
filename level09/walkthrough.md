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
    <+80>:	callq  0x9cd <set_username>
    <+85>:	lea    -0xc0(%rbp),%rax
    <+92>:	mov    %rax,%rdi
    <+95>:	callq  0x932 <set_msg>
}
```

```
set_username()
{
    <set_username+54>:	callq  0x555555554730 <puts@plt>
    >: Enter your username
    
    <set_username+92>:	lea    -0x90(%rbp),%rax
    <set_username+99>:	mov    $0x80,%esi
    <set_username+107>:	callq  0x555555554770 <fgets@plt>
   
    <+168>:	movzx  eax,BYTE PTR [rbp+rax*1-0x90]            rbp-0x90 + rax
    
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
    <set_msg+148>:	callq  0x555555554720 <strncpy@plt>     strncpy($rbp-0x400, )
}
```

secret_backdoor() isnt called anywhere, which has system


