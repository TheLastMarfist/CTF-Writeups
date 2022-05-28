# ultrasecure
## by Alexander Marx
### Task
Our task is to input the correct strings so we can get the program to print out the flag for us

### Solution
First we decompile the binary in ghidra. From this we see that main runs a function called ```password_check()``` which in turn runs a function called ```print_flag()```. If we look closer at ```password_check()``` we see that it runs two checks and if both pass, then it prints out the flag. Looking at the first check we see that it prompts us to enter something that it gives to us within a certain amount of time. The time is short enough where we could not do it by hand, so we will have to use a script. The next check asks us to input something, with that something being the value ```0xdeadb33f```. Once that is entered in, the flag will be printed.

### Script
```python
from pwn import *

io = remote("chal.ctf-league.osusec.org", 4545)
#io = process("./ultrasecure")

io.recvuntil("less than .05s: ")
number = io.recv()

io.sendline(number)
print(number)

io.recvline()

usrPass = str(int("-0x21524cc1", 16))
io.sendline(usrPass)

#io.interactive()
print(io.recvline())
```

### Flag
```osu{d3c0mp1ler_go_brrrr}```
