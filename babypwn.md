# babypwn
## by Alexander Marx
### Decompilation
From decompiling the binary file we can see that in main there is only one function call happening, the one to user_input(). By looking at that function we can see that it just asks the user to enter up to 200 characters. However, the local variable the user string is being stored in is a lot smaller than the character limit specified in the fgets call. This means that we can potentially overwrite data by entering enough character into the fgets.

By looking through the other funcitons we find a print_flag() function. Looking at it we can see that it simply does as its called: it prints out the flag. However, this function is not called by main or any other function called by main. This means that we will have to use the overflow vulnerability to redirect the program flow to that function if we want the flag.

### Exploitation
There are two parts to solving this. The first is to get the memory address of the print_flag() function and the second is to overwrite some data to redirect the program to that memory address.

To do the first, we start with running the line `pwn checksec babypwn` on the command line. This allows us to see the binary security settings of the file. The two importants things to notice is that there is no canary and no PIE. The canary will come into play when we try to redirect the program but the PIE comes into play now. The lack of a PIE means that the memory address of print_flag() will be the same everytime the program runs. So all we have to do is grab the memory address from the file locally and we are good.

To do the second, we have to understand how a function call in C interacts with the stack.
