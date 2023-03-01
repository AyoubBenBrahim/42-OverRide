
```
objdump -t level01 | grep bss
0804a040 g     O .bss	00000064              a_user_name

objdump -t level01 | grep text
080484d0 g     F .text	000000e6              main
08048464 g     F .text	0000003f              verify_user_name
```
