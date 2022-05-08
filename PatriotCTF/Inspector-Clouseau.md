# Patriot CTF

## Web

### Inspector Clouseau

**Difficulty:** Beginner

**Statement:** Can you help Inspector Clouseau find the secret message?

***

A simple inspection of the source code allows to get the flag :

```
<!DOCTYPE html>
<html>
<head>
  <title>Inspector Clouseau</title>
</head>
<body>
  <p>It is lovely weather we are having *wink*</p>
  <!-- PCTF{i_w0u1d_1ik3_t0_buy_4_h4mburg3r} -->
  <img src="https://c.tenor.com/eArRyUt12M8AAAAC/pink-panther.gif">
</body>
</html>
```

Flag: PCTF{i_w0u1d_1ik3_t0_buy_4_h4mburg3r}
