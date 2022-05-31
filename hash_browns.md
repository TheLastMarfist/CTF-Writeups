# hash_browns
## by Alexander Marx
### Task
The task was to find the flag inside of the webpage given to us

### Solution
The first step was to inspect the ```submit``` button. To do this we can either press ```f12``` then select the button in the html part or we can right click on the submit button and hit inspect. That will bring up the html that represents the button. There we can see what the button will do when it is clicked. Looking at that we could see that it calls the ```check_password()``` function. Now we need to track down this function. In html, code is usually held in script tags. When we scan the html brought up by the inspect, we find a script ```<script>``` lower down. In it we can see the ```check_password()``` function. Lets ook closer at that function. 

There we can see that what we enter in the password field gets hashed and then gets compared against a specific hash. If they equal then we log in. Now we need to figure out what password gives that specific hash. Lets run the hash through a cracker. The one I used was ```https://crackstation.net/```. This allows us to just paste in teh hash from the function and we will get the password. The password ends up being ```pineapple```. Now we can enter this in, which gives us a bouncing string that says get the flag. Now we could try to be cool and figure out how to get it through inspect element or we could be gamers and user our gamer reflexes and click the string. Doing this changes the string into our flag, so all we have to do is ```Ctrl+A``` and ```Ctrl+C``` to get it.

### Flag
```osu{p1n34ppl3_h45h_Br0wN5_4r3_g00D}```
