# CTF Midnight Flag : INFEKTION

## Stéganographie

### Challenge : Nothing is classified

***

Le challenge débute avec cette image :

![image](https://user-images.githubusercontent.com/49941629/165689108-f76cdf3c-f460-4e4a-9b76-64ad15b2d16e.png)

En modifiant la saturation et la teinte chroma de l'image, on peux apercevoir une chaine en base64 :

![image](https://user-images.githubusercontent.com/49941629/165690706-ba35a4fd-56a7-48c7-a78e-2d91016edeb9.png)

Je décode ensuite la chaine base64 :

```
echo -n "YW50aW1pc3NpbGVkZWZlbnNl" | base64 --decode
```

Grâce à cette commande, je récupère en output la valeur **antimissiledefense**

Cette valeur ne correspondant pas au flag, je suppose donc qu'elle nous servira à récupérer la véritable information.

Je décide d'utiliser l'outil steghide qui permet de cacher ou d'extraire des données dans des fichiers images ou audio.

Je lance donc la commande suivante avec l'utilisation de la passphrase durant le mode interactif : **antimissiledefense**

```
steghide extract -sf classified.jpeg
```

L'outil extrait les données et génère le fichier **flag.txt**.

Il ne me reste plus qu'à afficher le contenu du fichier pour récupérer le flag :

```
cat flag.txt
```

Flag : MCTF{cL@$Sif1eD_D0cUm3N7}
