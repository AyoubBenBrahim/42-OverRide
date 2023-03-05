
```
ssh level03@10.12.100.228 -p 4242
Hh74RPnuQ9sa5JAEXgNWCqz7sXGnh5J5M9KfPg3H
```
```
(gdb) info func

0x08048617  get_unum
0x0804864f  prog_timeout
0x08048660  decrypt
0x08048747  test
0x0804885a  main

```


p 0x1337d00d - input = output

test <+21>:	cmpl   $0x15,-0xc(%ebp) // output

jump if above

checking if the value at ebp-0xc is greater than 0x15, and if so, it jumps 
