# Patriot CTF

## Web

### Rock and Roll

**Difficulty:** Beginner

**Statement:** The website is empty! I can't see anything. Can you help me find what is hidden?

***

The site being empty, a simple header check with curl gives us the flag: `curl -I chal2.pctf.competitivecyber.club:49162`

```                                                                                             
HTTP/1.1 200 OK
Server: Werkzeug/2.1.1 Python/3.10.4
Date: Fri, 29 Apr 2022 21:06:12 GMT
Content-Type: text/html; charset=utf-8
link: <style.css>; rel=stylesheet;
flag: PCTF{r1Ck_D0wn_7h3_r0ll}
Refresh: 5; url=https://www.youtube.com/watch?v=dQw4w9WgXcQ
Content-Length: 0
```

Flag: PCTF{r1Ck_D0wn_7h3_r0ll}
