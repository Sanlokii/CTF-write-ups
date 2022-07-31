# TFC CTF

## WEB

### ROBOTS AND MUSIC

**Difficulty:** Warmup

**Statement:** Do you like old music?

***

The title of the challenge suggests that the **robots.txt** file is present on the web server.

```
User-agent: *
Disallow: /g00d_old_mus1c.php
```

It contains an interesting information, the path of the file `/g00d_old_mus1c.php`

This PHP page contains the flag !

![image](https://user-images.githubusercontent.com/49941629/182029991-2efd5b4e-85ba-4aa6-b537-d211288dab43.png)

Flag: TFCCTF{Kr4ftw3rk_4nd_th3_r0b0ts}
