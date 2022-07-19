# Notes 
**Web, 100**

### Prompt
We were using our professor's S3 bucket for free storage for our warez, and to steal any secrets he had uploaded, but he shut us out. We know that the credentials are still somewhere on the host

Our prof's username is professor  

Maybe we can find access again. username: aburns password: messwiththebest

http://alpha.tenable-ctf.io:9876/

### Solution 
Log in. The website shows a note-taking textbox that compiles LaTeX... :) That means we can do some [LaTeX hacking](https://0day.work/hacking-with-latex/). 
<img width="737" alt="Capture" src="https://user-images.githubusercontent.com/21980161/173691415-94554d9a-4f8c-4a40-861f-65d083115188.PNG">

The prompt tells us the professor username is *professor*, but we can also find it here. 
```latex
\input{/etc/passwd}
```
<img width="721" alt="Screenshot 2022-06-14 142636" src="https://user-images.githubusercontent.com/21980161/173692045-c66438dd-1664-462b-b00d-c9f05da66fc6.png">

Inferred from the prompt, our goal is to login as the professor. My first attempt was `\input{/home/professor/.ssh/<file_name>}`, but there's no luck. With some google search, we can find out that AWS credentials are stored under `~/.aws/credentials`. Now 
```latex
\input{/home/professor/.aws/credentials}
```
<img width="799" alt="Screenshot 2022-06-12 205700" src="https://user-images.githubusercontent.com/21980161/173691781-f07d2454-2dab-4d64-a133-a3eb64e6cc50.png">
Here they are. 

Connect to the S3 bucket. There's a certificate.pdf  
<img width="377" alt="Screenshot 2022-06-12 210130" src="https://user-images.githubusercontent.com/21980161/173692076-4f23d62a-da8e-4922-a802-b8645fa815e5.png">

Our flag is in there  
<img width="655" alt="Screenshot 2022-06-14 142820" src="https://user-images.githubusercontent.com/21980161/173692230-e8c813f1-20ab-426f-88b1-cc664346e4e5.png">
