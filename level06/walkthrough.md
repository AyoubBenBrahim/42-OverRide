
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
     
      exit
    }
    else
    {
      res = av1[3]
      
      res = res XOR 0x1337
      res = res + 0x5eeded
      ebp-0x10 = res
      ebp-0x14 = 0
      
      cmp    -0xc(%ebp),ebp-0x14
      
   0x0804885e <+278>:	cmp    -0xc(%ebp),%eax
   0x08048861 <+281>:	jl     0x804880f <auth+199>
      if (av2 > 0)
      {
      
      }
      
    }
  }
  
  
  
  
  
  
  
  
```

```
0x08048748 <+0>:	push   %ebp
   0x08048749 <+1>:	mov    %esp,%ebp
   0x0804874b <+3>:	sub    $0x28,%esp
   0x0804874e <+6>:	movl   $0x8048a63,0x4(%esp)
   0x08048756 <+14>:	mov    0x8(%ebp),%eax
   0x08048759 <+17>:	mov    %eax,(%esp)
   0x0804875c <+20>:	call   0x8048520 <strcspn@plt>
   0x08048761 <+25>:	add    0x8(%ebp),%eax
   0x08048764 <+28>:	movb   $0x0,(%eax)
   0x08048767 <+31>:	movl   $0x20,0x4(%esp)
   0x0804876f <+39>:	mov    0x8(%ebp),%eax
   0x08048772 <+42>:	mov    %eax,(%esp)
   0x08048775 <+45>:	call   0x80485d0 <strnlen@plt>
   0x0804877a <+50>:	mov    %eax,-0xc(%ebp)
   0x0804877d <+53>:	push   %eax
   0x0804877e <+54>:	xor    %eax,%eax
   0x08048780 <+56>:	je     0x8048785 <auth+61>
   0x08048782 <+58>:	add    $0x4,%esp
   0x08048785 <+61>:	pop    %eax
   0x08048786 <+62>:	cmpl   $0x5,-0xc(%ebp)
   0x0804878a <+66>:	jg     0x8048796 <auth+78>
   0x0804878c <+68>:	mov    $0x1,%eax
   0x08048791 <+73>:	jmp    0x8048877 <auth+303>
   0x08048796 <+78>:	movl   $0x0,0xc(%esp)
   0x0804879e <+86>:	movl   $0x1,0x8(%esp)
   0x080487a6 <+94>:	movl   $0x0,0x4(%esp)
   0x080487ae <+102>:	movl   $0x0,(%esp)
   0x080487b5 <+109>:	call   0x80485f0 <ptrace@plt>
   0x080487ba <+114>:	cmp    $0xffffffff,%eax
   0x080487bd <+117>:	jne    0x80487ed <auth+165>
   0x080487bf <+119>:	movl   $0x8048a68,(%esp)
   0x080487c6 <+126>:	call   0x8048590 <puts@plt>
   0x080487cb <+131>:	movl   $0x8048a8c,(%esp)
   0x080487d2 <+138>:	call   0x8048590 <puts@plt>
   0x080487d7 <+143>:	movl   $0x8048ab0,(%esp)
   0x080487de <+150>:	call   0x8048590 <puts@plt>
   0x080487e3 <+155>:	mov    $0x1,%eax
   0x080487e8 <+160>:	jmp    0x8048877 <auth+303>
   0x080487ed <+165>:	mov    0x8(%ebp),%eax
   0x080487f0 <+168>:	add    $0x3,%eax
   0x080487f3 <+171>:	movzbl (%eax),%eax
   0x080487f6 <+174>:	movsbl %al,%eax
   0x080487f9 <+177>:	xor    $0x1337,%eax
   0x080487fe <+182>:	add    $0x5eeded,%eax
   0x08048803 <+187>:	mov    %eax,-0x10(%ebp)
   0x08048806 <+190>:	movl   $0x0,-0x14(%ebp)
   0x0804880d <+197>:	jmp    0x804885b <auth+275>
   0x0804880f <+199>:	mov    -0x14(%ebp),%eax
   0x08048812 <+202>:	add    0x8(%ebp),%eax
   0x08048815 <+205>:	movzbl (%eax),%eax
   0x08048818 <+208>:	cmp    $0x1f,%al
   0x0804881a <+210>:	jg     0x8048823 <auth+219>
   0x0804881c <+212>:	mov    $0x1,%eax
   0x08048821 <+217>:	jmp    0x8048877 <auth+303>
   0x08048823 <+219>:	mov    -0x14(%ebp),%eax
   0x08048826 <+222>:	add    0x8(%ebp),%eax
   0x08048829 <+225>:	movzbl (%eax),%eax
   0x0804882c <+228>:	movsbl %al,%eax
   0x0804882f <+231>:	mov    %eax,%ecx
   0x08048831 <+233>:	xor    -0x10(%ebp),%ecx
   0x08048834 <+236>:	mov    $0x88233b2b,%edx
   0x08048839 <+241>:	mov    %ecx,%eax
   0x0804883b <+243>:	mul    %edx
   0x0804883d <+245>:	mov    %ecx,%eax
   0x0804883f <+247>:	sub    %edx,%eax
   0x08048841 <+249>:	shr    %eax
   0x08048843 <+251>:	add    %edx,%eax
   0x08048845 <+253>:	shr    $0xa,%eax
   0x08048848 <+256>:	imul   $0x539,%eax,%eax
   0x0804884e <+262>:	mov    %ecx,%edx
   0x08048850 <+264>:	sub    %eax,%edx
   0x08048852 <+266>:	mov    %edx,%eax
   0x08048854 <+268>:	add    %eax,-0x10(%ebp)
   0x08048857 <+271>:	addl   $0x1,-0x14(%ebp)
   0x0804885b <+275>:	mov    -0x14(%ebp),%eax
   0x0804885e <+278>:	cmp    -0xc(%ebp),%eax
   0x08048861 <+281>:	jl     0x804880f <auth+199>
   0x08048863 <+283>:	mov    0xc(%ebp),%eax
   0x08048866 <+286>:	cmp    -0x10(%ebp),%eax
   0x08048869 <+289>:	je     0x8048872 <auth+298>
   0x0804886b <+291>:	mov    $0x1,%eax
   0x08048870 <+296>:	jmp    0x8048877 <auth+303>
   0x08048872 <+298>:	mov    $0x0,%eax
   0x08048877 <+303>:	leave
   0x08048878 <+304>:	ret
```










