# maldiant
## by Alexander Marx
### Task
The task was to unencrypt the flag png that was encrypted by a pdf.

### Solution
The first step was to download some applications. They were pyinstxtractor, python-uncompyle6, and peepdf. The links to get them are https://github.com/extremecoders-re/pyinstxtractor, https://github.com/rocky/python-uncompyle6, and https://github.com/jesparza/peepdf respectively.

Once these are installed, the next step is to run peepdf on the pdf. To do this run the command `python <path to peepdf.py> -i <path to pdf>`. It is important to note that peepdf will only run on python 2. This will open peepdf in interactive mode and will print a lot of information to the console. If it doesn't run the `info`. What you are looking for is where peepdf prints out the objects, streams, and suspicious elements. To get the name of the embedded malware, find the number under Names in the suspicious elements and run the command `object <number>`. This will print out the name of the embedded file. The name in question is `not_odysseus`. Then find the number under EmbeddedFile in the suspicious elements and run the command `stream <number> > output`. This will extract the data where the embedded file is located and stores it in a file named output. Type `quit` to exit peepdf.

Next, we will run pyinstxtractor on the output file. To do this run `python pyinstxtractor.py output`. This works on python 3. This will create a folder will all the different python files from the stream in it. You will notice the file `not_odysseus.pyc` in it. This is our malware program in python bytecode.

The next step would be to run uncompyle6 on that file. To do this run `uncompyle6 <path to not_odysseus.pyc>` however, uncompyle6 only works with python 3.8 and not 3.9 so be aware of that. The python program you should get is 
```python 
# uncompyle6 version 3.8.0
# Python bytecode 3.8.0 (3413)
# Decompiled from: Python 3.8.10 (default, Sep 28 2021, 16:10:42) 
# [GCC 9.3.0]
# Embedded file name: not_odysseus.py
from itertools import *
import os
if 'yes_please_remove_guardrail_friendo' not in os.environ.keys():
    print('GUARDRAIL TRIPPED: This is probably a good thing!!!!!!!')
    exit(1)

def encrypt_and_destroy(filename):
    try:
        with open(filename, 'rb') as (f):
            raw = f.read()
        magic_bytes = raw[:8]
        print(magic_bytes)
        enc = [a ^ b for a, b in zip(raw, cycle(magic_bytes))]
        with open(f"{filename}.MALD", 'wb+') as (enc_f):
            enc_f.write(bytes(enc))
        os.remove(filename)
    except FileNotFoundError:
        print('Bad file, please try again!')


encrypt_and_destroy('flag.png')
# okay decompiling not_odysseus.pyc
```

Essentially what this program does is open the file, get the first 8 bytes of it and XOR each byte in the file by those first 8. That means that all we have to do to unencrypt the png is to xor the encrypted png by those 8 bytes. Luckily all pngs have the exact same first 8 bytes. They are `137, 80, 78, 71, 13, 10, 26, 10`. When you XOR each byte in the encrypted png by those 8 bytes and then write out the bytes to a new file, a png is created that is an image of the flag. Just open up the png and you have the flag.

### Python Script

```python 
from itertools import *

with open ("./maldiant/flag.png.MALD", 'rb') as (f):
    raw = f.read()

magicBytes = [137, 80, 78, 71, 13, 10, 26, 10]
dec = [a ^ b for a, b in zip(raw, cycle(magicBytes))]

with open("flag.png", 'wb+') as (sol):
    sol.write(bytes(dec))
```

### Flag

`osu{M@G1C_ByT3S_AR3_N3At}`
