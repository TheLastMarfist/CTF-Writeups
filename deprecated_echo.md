# deprecated_echo
## by Alex Marx
### Task
The task was to find the information held by the image file.

### Solution
The first step was to download the tool at `https://github.com/Aqcurate/lsb-steganography`. Once the tool is installed run the command 
`python <path to steglsb.py> -d <path to input file> 3 <output filename>`. It is important to note that the number 3 is entered there because of the information given that Bits = 3 and that the output filename needs to end with the same extension as the input file, otherwise this won't work. Running this command will output another image file which when opened displays the following url in the top left corner: `https://pastebin.com/9bRN2Eah`. Following the link gives the following python code:
```python
#!/usr/bin/env python2
 
if __name__ == '__main__':
 
    try:
        # Get User Input and file  as Strings
        user_input = raw_input("Enter a string:")
        user_file = raw_input("Enter a location in the current directory:")
 
        # Check Directory
        if ['.', '/'] in user_file:
            raise ValueError("Bruh")
 
        # Write user input to a file
        if user_input.isalpha():
            f = open(user_file, 'w')
            f.write(user_input)
            f.close()
 
        # Return the input to the user
        print user_input
 
    # Catch-all for exceptions
    except Exception as exc:
        print "Whoops, something happened :("
 
        # Dump the exception to somewhere
        file_location = input("Where should I dump crash log to?:")
        
        # Check file path is valid
        if ['.', '/'] in user_file:
            f = open(file_location, 'w')
            f.write(str(exc))
            f.close()
```
The important thing to note is how the program is getting input from user, namely the difference between `input()` and `raw_input()`. `raw_input()` only takes in a string from the user but `input()` evaluates the string entered by the user first. That means that we can enter python code in and get the program to run it. We were told that the flag is stored in flag.txt which means we somehow have to get the content of that file to print to the screen. It is important to realize that if we enter a string that has some kind of error in it, the program will output the error it encountered. So to solve this challenge, first we run `nc chal.ctf-league.osusec.org 6969`. Then once we have connected, enter in `asdf` to the first two prompts. Then for the third prompt enter in `execfile("flag.txt")`. This will call an error and in the error message the flag will be printed.

### Flag
`osu{m1sc_CTF_b35t_cTF}`
