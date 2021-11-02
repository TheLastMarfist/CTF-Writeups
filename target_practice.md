# target_practice
## by Alexander Marx
### Task
The task we were given was to find the real name of the person using the username `anonhunter26`

### Solution
The first step to take is to find out any information we can about anonhunter26. One way of doing this is to use a tool that looks up usernames. The one I used was What's My Name at https://whatsmyname.app/. That gave a link to a twitter account with the same username. The user is shadowbanned so the link from What's My Name doesn't work, but to get to the account you can search up the username in twitter and from there you can access it. In one of anonhunter26's tweets they mention a coworker with the username hatebav2ropc. Looking at hatbav2ropc's twitter doesn't turn up anything so we turn back to What's My Name to see if that username corresponds to any other accounts. Doing that search turns up a GitHub account. The GitHub account has a repository so we clone it and run a git log on the cloned repository. That turns up the email that corresponds with the hatebav2ropc account: anonymousfreak32@gmail.com.

The next step is to figure out any information we can about the email. Using an email lookup tool like the one available at https://tools.epieos.com/ gives us multiple pieces of information, one of which is the full name of the person who uses the email: Gabriel Cortney. Generally, people make usernames for social media accounts that are some combination of their name, so what we do is run What's My Name on different username combinations of Gabriel Cortney. Eventually the username gabriel_cortney turns up a twitter account. Similarly to anonhunter26, this account is shadowbanned so we search up the username in twitter to get to the account.

Looking at the profile of Cortney's account we see that he is a lead security analyst at opticalsocial. We can conclude from this that anonhunter26 also works at opticalsocial. Looking at the twitter account for optical social doesn't turn up anything so we do a google search for the company (use quotes around opticalsocial). The first link after we do this is a GitHub account where the username is Oswald-Denman. In the profile of the account we can see that they are a senior software developer at opticalsocial. If we look back at anonhunter26's twitter profile we can see that they are a senior software developer at a small startup. Opticalsocial is a small startup so we can reasonably assume that Oswald Denman is the full name of anonhunter26.

### Flag
`$submit osu{Oswald_Denman}`
