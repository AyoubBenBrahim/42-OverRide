
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
  
  login_len = ebp-0xc = strlen(av1, 0x20)
  cmpl   $0x5,-0xc(%ebp)
  
  if (login_len > 5)
  {
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
      
        hash = hash XOR 0x1337
        hash = hash + 0x5eeded
        ebp-0x10 = hash
        ebp-0x14 = 0
    
      int hash = (login[3] ^ 0x1337) + 0x5eeded

      
        <+278>:	cmp    -0xc(%ebp),ebp-0x14
        if (av2 > 0)
        {
         <+199>
         res = av1[ebp-0x14]
         <+208>:	cmp    $0x1f,%al   compares the least significant byte of the value in the EAX to 0x1f (31)
      
       }
        else if (ebp+0xc == ebp-0x10) // serial == hash
        {
           return 0;
        }
        else 
          return 1
    }
  }
```

```
well it does some other hashing thing with our hash stored at -0x10(%ebp)
then comparse it with argv2 aka input serial

 0x08048863 <+283>:   mov    0xc(%ebp),%eax
 0x08048866 <+286>:   cmp    -0x10(%ebp),%eax
 
 return (serial != hash);
 
 ```
 so the hash is already calculated by the program based on our input login
 
 we only need to dig it out by gdb
```
first we need to bypass ptrace blocking

   0x80487b5 <auth+109>:        call   0x80485f0 <ptrace@plt>
=> 0x80487ba <auth+114>:        cmp    $0xffffffff,%eax
   0x80487bd <auth+117>:        jne    0x80487ed <auth+165>

(gdb) i r
eax            0xffffffff  -1

gdb> b *0x080487ba
gdb> b *0x08048866    <+286>:   cmp    -0x10(%ebp),%eax

(gdb) info break
2       0x08048941 <main+200>  get login+serial

4       0x080487ba <auth+114>  bypass ptrace
5       0x08048866 <auth+286>  inspect hash

run

-> Enter Login: aybouras
-> Enter Serial: serial

(gdb) c
Continuing.
=> 0x80487ba <auth+114>:        cmp    $0xffffffff,%eax

(gdb) set $eax=0
(gdb) c
Continuing.
=> 0x8048866 <auth+286>:        cmp    -0x10(%ebp),%eax

(gdb) x/s $ebp-0x10
0xffffd698:      "m!_"
```
ok, i forgot that scanf takes the serial as %u
```py

(gdb) x/wx $ebp-0x10
0xffffd698:     0x005f216d
(gdb) p 0x005f216d = 6234477

or simply
(gdb) p *(unsigned int *)($ebp-0x10)
$4 = 6234477


-> Enter Serial: 6234477
Authenticated!
$ pwd
/home/users/level06
$ cat /home/users/level07/.pass
GbcPDRgsFK77LNnnuh7QyFYA2942Gp8yKj9KrWD8
```




