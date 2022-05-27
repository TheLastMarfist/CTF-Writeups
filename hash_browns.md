# hash_browns
## by Alexander Marx
### Task
The task was to find the flag inside of the webpage given to us

### Solution
The first step was to inspect the ```submit``` button. Looking at that we could see that it calls the ```check_password()```. Then we look at that function in the script tag in the html. There we can see that the hash of the password we enter has to equal a specific hash. This allows us to crack the hash, giving us the password ```pineapple```. Now we can enter this in, which gives us a bouncing string that says get the flag. Now we could try to be cool and figure out how to get it through inspect element or we could be gamers and user our gamer reflexes and click the string. Doing this changes the string into our flag, so all we have to do is ```Ctrl+A``` and ```Ctrl+C``` to get it.

### Flag
```osu{p1n34ppl3_h45h_Br0wN5_4r3_g00D}```
