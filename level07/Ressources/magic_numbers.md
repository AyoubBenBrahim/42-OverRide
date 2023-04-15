

Unsigned Divide by 3

'Assembly Optimization Tip


 a "magic number:" a number that lets you substitute speedy multiplication for pokey division, as if by magic.



Multiply to divide.

If you have a full 32-bit number and you need to divide, you can simply do a multiply and take the top 32-bit half as the result. This is faster because multiplication is faster than division.

Some C/C++ compilers do this automatically when optimization is turned on

If you want to divide by the odd number, and you know the number you want to divide is divisible without remainder, you can do this by multiplying by "magic" number and take the LOW half of the result, without need to do any shifts etc.

This approach is based on the theory of 2-adic numbers

https://web.archive.org/web/20210618232634/http://www.asmcommunity.net/forums/topic/?id=21308



the magic number 0xaaaaaaab

https://aakinshin.net/posts/perfex-div/


The principle of the magic number in method 3 is to implement the division operation of 32-bit divisor by multiplication. the compiler will multiply the divisor by a 32-bit reciprocal to form a 64-bit number, among them, the low 32-bit is the remainder, and the high 32-bit is the result we need. Below are some common magic numbers. For example, 0xaaaaaaab represents 2/3, and 0xcccccccd represents 4/5, you can search for related content on the Internet. 
https://topic.alibabacloud.com/a/unsigned-integer-division-using-shift-and-additionsubtraction-operations_8_8_31838351.html





2^32 / 3 = 0x55555555

Turns out this isn't very precise due to roundoff errors, so what people did was to increase the range to 33 bits and round up, and then right shift 'edx' by 1 (to divide by 2^33 instead of 2^32). For example (the +1 is due to rounding up the .(6) fraction):

2^33 / 3 + 1 = 0xAAAAAAAB

Code:
; eax = your number
mov edx, 0xAAAAAAAB
mul edx
shr edx, 1
; edx = output, eax/3    
https://board.flatassembler.net/topic.php?t=18975







if we multiply any integer N divisible by 3 by such infinite chain, we will get N/3. 

The first 32 bits of infinite 1/3 expansion are 0AAAAAAAB

https://board.flatassembler.net/topic.php?t=18975





The general method is:
1. Multiply n by a certain magic number.
2. Obtain the high-order half of the product and shift it right some
number of positions from 0 to 31.
3. Add 1 if n is negative.

the boaring math behind magic numbers
https://ridiculousfish.com/blog/posts/labor-of-division-episode-i.html



