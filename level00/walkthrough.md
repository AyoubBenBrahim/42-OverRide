```
level00@OverRide:~$ ./level00
***********************************
* 	     -Level00 -		  *
***********************************
Password:
```

i assume that there would be a cmp + jump instruction with the correct pwd

```asm
   0x080484e7 <+83>:	cmp    $0x149c,%eax
   0x080484ec <+88>:	jne    0x804850d <main+121>
   0x080484ee <+90>:	movl   $0x8048639,(%esp)
   0x080484f5 <+97>:	call   0x8048390 <puts@plt>
   0x080484fa <+102>:	movl   $0x8048649,(%esp)
   0x08048501 <+109>:	call   0x80483a0 <system@plt>
```

p/d 0x149c = 5276

```asm
Password:5276

Authenticated!
$ pwd
/home/users/level00
$ whoami
level01
$ cat /home/users/level01/.pass
uSq2ehEGT6c9S24zbshexZQBXUGrncxn5sD5QfGL
```

