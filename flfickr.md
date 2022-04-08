# flfickr
## by Alexander Marx
### Task
The task we were given was to retrieve the flag from by exploiting the web application that allows you to upload photos and view them.

### Solution
The first step we need to take is to figure out what the vulerability of this web app is. The name is a hint to what it is (an LFI vulnerability), but this can also be seen in the way that when you switch languages, the web app directly gets the language file from the folder ```languages```.

Now that we have the vulnerability we need to exploit it. Since the funcitonality of the web app is all about uploading and viewing pictures it is safe to assume that the path to exploitation lies within that part. This ends up being true. Since the web app straight up loads the image into the html document, if we can somehow imbed php code within the image through a php tag, the web app will treat it as if it were a normal script tag and will end up running the code. To add the code that we need to the image we will use exiftool. With it we can use the command ```exiftool -comment="[payload]" [image]``` to put our payload in the comment field of the image. Since we need to find where the flag is located, we need to search for it. To do that we will use the ```find``` command. Since find is a command line command, we will use the php function ```exec()``` to run it. We also can't forget to format it as an html php tag so that our code will actually be run. With all of this in mind, our payload ends up being ```<?php $output1 = exec('find / -name flag.txt'); echo $output1; ?>```.
