# secure-encryptor
## by Alexander Marx
### Task
Our task is to break the encryption on the flag

### Solution
From running the program we can see that it gives us the encrypted flag, and then prompts us for a plaintext, which it then encrypts and gives us the ciphertext. We know that this challenge uses a OTP algorithm to encrypt both strings. However, they use the same OTP for both encryptions. This gives us an attack vector. Looking at the decompilation, we can see that when the function encrypts something, it XORs it by the OTP as well as running it through ```memfrob()```. Memfrob just XORs by 42. So essentially our goal is to be able to XOR the flag ciphertext by 42 and the OTP to get it. To get the OTP we first want to enter in a plaintext. We can see that the program accepts plaintexts of 1024 characters, but it pads it if its too low. That means we want to enter a plaintext that is 1024 characters long so it doesn't get padded at all.

When we enter it, we get a ciphertext back. We know that the ciphertext is the plaintext XOR 42 XOR OTP. However we can reverse an XOR operation by XOR the output by the same thing we XORed the input by (a XOR b = c, c XOR b = a). This means we can get the OTP by XORing out ciphertext by 42 and then also by the plaintext we entered in.

Now that we have the OTP, we can undo the encryption on the encrypted flag by XORing by 42 and the OTP we found. Then we just need to print the output of that and find the flag within all the garbage.

### Script
```python
from pwn import *
import base64

io = remote("chal.ctf-league.osusec.org", 4646)

io.recvline()
encryptFlag64 = io.recvuntil("==")
encryptFlag = base64.b64decode(encryptFlag64)

io.recvuntil("for you: ")
usrInput = "a" * 1024
io.sendline(usrInput)

io.recvline()
encryptUser64 = io.recvuntil("==")
encryptUser = base64.b64decode(encryptUser64)
getOrd = ['a'] * 1024
for i in range(1024):
    getOrd[i] = encryptUser[i] ^ ord("a") ^ 42
flag = ['a'] * 1024
for i in range(1024):
    flag[i] = encryptFlag[i] ^ 42 ^ getOrd[i]

for i in range(1024):
    print(chr(flag[i]), end="")
```

### Flag
```osu{d0n7_u5e_4_1_71m3_p4d_m0r3_th4n_0nc3}```
