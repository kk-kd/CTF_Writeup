# Notes 
**Web, 100**

### Prompt
We were using our professor's S3 bucket for free storage for our warez, and to steal any secrets he had uploaded, but he shut us out. We know that the credentials are still somewhere on the host

Our prof's username is professor  

Maybe we can find access again. username: aburns password: messwiththebest

http://alpha.tenable-ctf.io:9876/

### Solution 
Log in. The website shows a note-taking textbox that compiles LaTeX... :) That means we can do some [LaTeX hacking](https://0day.work/hacking-with-latex/). 

```latex
\input{/etc/passwd}
```

The prompt tells us the professor username is *professor*, but we can also find it here. 

Inferred from the prompt, our goal is to login as the professor. My first attempt was `\input{/home/professor/.ssh/<file_name>}`, but there's no luck.


With some google search, we can find out that AWS credentials are stored under `~/.aws/credentials`. Now 

```latex
\input{/home/professor/.aws/credentials}
```
Here they are. 

Connect the S3 bucket. There's a certificate.pdf. 