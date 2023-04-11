
```asm
0x08048720  enable_timeout_cons
0x08048748  auth
0x08048879  main
```

```asm
 0x080488c2 <+73>:	mov    $0x8048b08,%eax           "-> Enter Login: "
 0x080488ca <+81>:	call   0x8048510 <printf@plt>

 0x080488d8 <+95>:	movl   $0x20,0x4(%esp)
 0x080488e0 <+103>:	lea    0x2c(%esp),%eax
 0x080488e4 <+107>:	mov    %eax,(%esp)
 0x080488e7 <+110>:	call   0x8048550 <fgets@plt>   fgets(esp+0x2c, 32, stdin)
 
 0x08048910 <+151>:	mov    $0x8048b40,%eax         "-> Enter Serial: "
 0x08048918 <+159>:	call   0x8048510 <printf@plt>
 
  0x0804891d <+164>:	mov    $0x8048a60,%eax
  0x08048922 <+169>:	lea    0x28(%esp),%edx
  0x0804892d <+180>:	call   0x80485e0 <__isoc99_scanf@plt>     scanf("%u", esp+0x28)
  
  ret = auth(esp+0x2c, esp+0x28) ==> auth(login, serial)
  
  if (ret =! 0)
  {
    puts("Authenticated!")
    system("/bin/sh")
  }
  
  =====
  
  ebp-0xc = strlen(av1, 0x20)
  cmpl   $0x5,-0xc(%ebp)
  
  if (len > 5)
  {
      0x08048796 <+78>:	movl   $0x0,0xc(%esp)
      0x0804879e <+86>:	movl   $0x1,0x8(%esp)
      0x080487a6 <+94>:	movl   $0x0,0x4(%esp)
      0x080487ae <+102>:	movl   $0x0,(%esp)
   
      eax = ptrace(int request, pid_t pid, caddr_t addr, int data)
      eax = ptrace(0, 0, 1, 0)
    
      if (eax == 0xffffffff)
      {
        puts "!! TAMPERING DETECTED !!"
     
        exit()
      }
      else
      {
        res = av1[3]
      
        res = res XOR 0x1337
        res = res + 0x5eeded
        ebp-0x10 = res
        ebp-0x14 = 0
    
      
        <+278>:	cmp    -0xc(%ebp),ebp-0x14
        if (av2 > 0)
        {
         <+199>
         res = av1[ebp-0x14]
         <+208>:	cmp    $0x1f,%al   compares the least significant byte of the value in the EAX to 0x1f (31)
      
       }
        else if (ebp + 0xc == ebp-0x10)
        {
           return 0;
        }
        else 
          return 1
      
    }
  }
  
  
  
  
  
  
  
  
```



```
...............
```










