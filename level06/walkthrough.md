
```
0x08048720  enable_timeout_cons
0x08048748  auth
0x08048879  main
```

```
 0x080488c2 <+73>:	mov    $0x8048b08,%eax           "-> Enter Login: "
 0x080488c7 <+78>:	mov    %eax,(%esp)
 0x080488ca <+81>:	call   0x8048510 <printf@plt>

 0x080488d8 <+95>:	movl   $0x20,0x4(%esp)
 0x080488e0 <+103>:	lea    0x2c(%esp),%eax
 0x080488e4 <+107>:	mov    %eax,(%esp)
 0x080488e7 <+110>:	call   0x8048550 <fgets@plt>   fgets(esp+0x2c, 32, stdin)
 
 0x08048910 <+151>:	mov    $0x8048b40,%eax         "-> Enter Serial: "
 0x08048918 <+159>:	call   0x8048510 <printf@plt>
 
  0x0804891d <+164>:	mov    $0x8048a60,%eax
  0x08048922 <+169>:	lea    0x28(%esp),%edx
  0x0804892d <+180>:	call   0x80485e0 <__isoc99_scanf@plt> scanf("%u", esp+0x28)
  
  ret = auth(esp+0x2c, esp+0x28) ==> auth(login, serial)
  
  if (ret =! 0)
  {
    puts("Authenticated!")
    system("/bin/sh")
  }
  
  
  
  
  
  
  
  
  
  
```










