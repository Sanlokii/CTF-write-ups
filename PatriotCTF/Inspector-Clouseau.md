# Patriot CTF

## Web

### Inspector Clouseau

**Enoncé :** Pouvez-vous aider l'inspecteur Clouseau à trouver le message secret ?

***

Une simple inspection du code source permet de récupérer le flag :

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

FLAG : PCTF{i_w0u1d_1ik3_t0_buy_4_h4mburg3r}
