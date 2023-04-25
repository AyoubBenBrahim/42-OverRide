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
