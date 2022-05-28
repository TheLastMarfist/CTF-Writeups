# windy_city
## by Alexander Marx
### Task
Our task is to find the artist behind the mural on the left side of the building in the image

### Solution
Our first goal is to figure out where the picture was taken. To do this we want to reverse image search it. We will be using an extension called ```Search by Image```. This allows us to run the image through multiple reverse image search sites to get as many results as possible. However, when we reverse image search the entire image, nothing pops up. That means that we should start reverse image searching different portions of the image. The portion that ends up giving us something is the door. The specific site that returns something when we run the tool is bing which points us to a StreetArtNews article that gives us the address of the image: ```South Shore 1706-8 E. 79th street```.

Using that address we can go on a trip through google maps. The building has changed since the time when the original picture was taken, but by looking at the surroundings we can figure out which building is correct. From there we can find the mural that is on the left side of the building. We then take a screenshot of it and go right back to our reverse image search extension. By cropping the image as needed yandex eventually turns up a tweet about the mural. The tweet mentions an artist whose name is Max Sansing. When we submit that name, we end up getting the correct flag.

### Flag
```osu{max_sansing}```
