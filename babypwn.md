# babypwn
## by Alexander Marx
### Decompilation
From decompiling the binary file we can see that in main there is only one function call happening, the one to user_input(). By looking at that function we can see that it just asks the user to enter up to 200 characters. However, the local variable the user string is being stored in is a lot smaller than the character limit specified in the fgets call. This means that we can potentially overwrite data by entering enough character into the fgets.

By looking through the other funcitons we find a print_flag() function. Looking at it we can see that it simply does as its called: it prints out the flag. However, this function is not called by main or any other function called by main. This means that we will have to use the overflow vulnerability to redirect the program flow to that function if we want the flag.

### Exploitation
There are two parts to solving this. The first is to get the memory address of the print_flag() function and the second is to overwrite some data to redirect the program to that memory address.

To do the first, we start with running the line `pwn checksec babypwn` on the command line. This allows us to see the binary security settings of the file. The two importants things to notice is that there is no canary and no PIE. The canary will come into play when we try to redirect the program but the PIE comes into play now. The lack of a PIE means that the memory address of print_flag() will be the same everytime the program runs. So all we have to do is grab the memory address from the file locally and we are good.

To do the second, we first have to understand how the function call interacts with the stack. Since user_input() has no parameters, nothing funky will happen with the stack there. Since a function call occcurend, the return address will be passed to the stack and then the stack pointer will be passed as well. Then since user_input() has to local variables, the function passes those to the stack as well. The way to exploit this is to write enough data so that the return address gets overwritten. Since we can find the address of the print_flag() function we should be able to overwrite the return address with the print_flag() one. To do this we have to figure out how much data to pass to the stack before we pass the address. Since both local variables are 8 bytes and the stack pointer is also 8 bytes, we need to pass 24 bytes of data before we pass the address. One way to do this would be to just pass a string of 24 characters and then the address. Once we do this the function print_flag() will be called so all that needs to be done after is to just print out the flag.

### Python Script

```python
from pwn import *

# get address

e = ELF("./babypwn")
addr = e.symbols["print_flag"]

print(
    f"Found the address to the function_name() function to be {hex(addr)}"
)

# exploit

p = remote("chal.ctf-league.osusec.org", 4747)
p.recvline()
padStr = "a" * 24

# send 24 bytes and address to server
# string converted to bytes so it can be sent together with converted addr
p.sendline(bytes(padStr, 'ascii') + p64(addr))

# get flag
flag = p.recvline();

# print flag
print(flag)

p.close()
```

### Flag

`osu{c0ngr4tz_on_F1r5T_pwn}`
