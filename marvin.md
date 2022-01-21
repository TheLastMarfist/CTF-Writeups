### marvin
## by Alexander Marx
# The Task
Our task was to leak the flag that was added to the discord bot marvin's database.

# Solution
The first step would be to join the linked discord so that we could interface with the old version of marvin. After that since we were given the source code, we should first look at it to see if we can identify any vulnerablilities. Since we will be interfacing with the bot through commands, we should look for any vulnerabilities there. Looking at the GitHub, we can see a file called ```Member.py```. Under closer examination this holds a list of commands members can run so this will be a good place to look for vulnerabilities. Looking at the commands, we can see that some of them run SQL commands using information that is directly entered by the user without examining it, meaning that the commands are vulnerable to SQL injection.
Now that we know what vulnerability we want to exploint, we need to figure out which command to SQL inject to leak the flag. Of the commands that are vulnerable like ```submit``` and ```info```, ```info``` returns the most information so we should probably focus our efforts on that command. Looking at the ```info``` command, we see that it takes in a challenge name and returns a lot of information about it. This means that we want the name of the challenge. To do this we can simply run ```$challenges``` and marvin will return to us the entire list of challenges. The most recent one is called marvin, so we can basically be sure that it is the correct challenge name. Now that we have the name, it is time to leak the flag.
Since we are given the source code of the SQL database we can see how the flag is stored. It is stored in the challenges table. This means that to grab the flag information for the marvin challenge the SQL code we need is: 
```SQL
SELECT flag FROM challenges WHERE name='marvin'
```
Now since the original ```info``` command uses a ```SELECT``` statement as well, we can use the ```UNION``` clause to add the information we want onto the ```info``` command. To successfully SQL inject this we run the command ```$info "' UNION SELECT flag FROM challenges WHERE name='marvin' ;"```. However this returns the error ```Command raised an exception: DataError: 1222 (21000): The used SELECT statements have a different number of columns```. The fix for this is pretty simple. All we have to do is make it so that after our ```Union``` clause, we are trying to grab the same number of collumns as the SELECT statement in the source code, that value being 6. That makes our command now be ```$info "' UNION SELECT chal_id, flag, points, name, download, access FROM challenges where name='marvin' ;"```. This will return the flag where the command would normally state the category the challenge is in.
# Flag
```osu{GOoD_lUCK_F1Nd1nG_7H15_M4rv1N_wOuLd_neVer_lE4k_4_fl4g}```
