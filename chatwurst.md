# chatwurst
## by Alexander Marx
### The Task
The task was to retrieve the messages of a group of hackers on the messaging app called chatwurst

### Solution
Since we were given the apk file for chatwurst, the first step was to use a decompiler to look at the source code. The decompiler I used was Jadx. When the apk file is opened in Jadx, under the ```com``` folder, we can see another folder called ```sinclustoapps.chatwurst```. This is probably a good place to start.

In that folder we can see a number of files, with one being called ```MainActivity```. Looking in that we can see a function for when a user is created called ```signupTapped```. In it, they call ```ChatwurstClient.createUser```, which calls a function from another file in the ```sinclustoapps.chatwurst``` folder. In the ```ChatwurstClient``` file we see a number of functions that make POST request to urls. This means that we can use those urls to make post requests of our own.

To do this I used the web application ReqBin at https://reqbin.com/. To use it put the url in the correct field, change the request type to POST in the drop down menu, and under the content header, set the drop down menu to JSON since the information in the functions are being sent as JSON objects. Now that we know how to use the application, we need to figure out how to use these functions to somehow gain access to the group chat the hackers are using. Looking through the functions, we can notice that they all use a hashmap called credential, which is the combination of a user's username and password to restrict those who can access the different groups and messages. So our first step should be to create our own account. Using the application, put in the url in the ```createUser``` function , and in the content area put in ```{"username": "your username", "password": "your password"}``` and hit send.

Now that we have created an account, it is time to figure out how to leak the messages. One thing that we can notice in the functions is that there is nothing being called to authenticate the credential. If there is no authentication going on the server when we send our POST request, that would mean that we can use our own username and password to look at groups and messages of other users. To test this we can use the ```getGroups``` function. To do this we put the url into the web application and in the content part we enter in ```{"credential": {"username": "your username", "password": "your password"}, "user_id": 1}```. When we hit send it returns a list of groups that the user with id 1 is part of. We know that the user FizzbuzzMcFlurry is part of the group of hackers, so to find his user id, all we have to do is change the value we send in until the username FizzbuzzMcFlurry is part of every group that is returned. That id ends up being 3.

Now that we have the id, as well as the list of groups FizzbuzzMcFlurry is part of, we need to look at the messages of those groups. To do this we use the function ```getMessages```. Similarly to before we want to send in our credentials but instead of a user id, we want to send in a group id. The format of the json is ```{"credential": {"username": "TheLastMarfist1", "password": "pword"}, "group_id": 1}```. Remember to change the url to the one in the ```getMessages``` function. Looking through the messages of groups 1 and 8 don't turn up anything, but when we look through group 9, we find the flag.

### Flag
```osu{cH4TWUrst_m0RE_L1KE_wUrSt_ch47}```
