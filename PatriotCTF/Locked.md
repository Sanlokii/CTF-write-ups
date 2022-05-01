# Patriot CTF

## Web

### Locked

**Difficulté :** Facile

**Enoncé :** Mon site web a été complètement sécurisé ! !! Peu importe à quel point tu es un bon hacker, tu ne pourras pas pirater mon site !!!

TODO : Mettre l'URL du site web.

***

Je commence par effectuer un fuzzing du site : `ffuf -w ~/fuzz-Bo0oM.txt -u http://chall1.pctf.competitivecyber.club:10017/FUZZ`

![image](https://user-images.githubusercontent.com/49941629/166079144-e2d4739c-8fd3-46b9-8675-7f05deb11730.png)

Grâce au fuzzing, je sais qu'il y a un répertoire **/admin**.

J'inspecte donc le code source du fichier **/admin/index.html** et je remarque un lien menant vers le fichier **main.js**.

Code source de **main.js** :

```
var pages = {
  home: "Welcome to my completely unhackable site. There are no flags here, so don\'t even bother trying to look for them lol.",
  about: "This site is 100% completely utterly unhackable, and anyone who says anything otherwise is an idiot who knows nothing about cybersecurity.",
  contact: "Do you want to get the flag? If you do, then contact me at hahahahah.jk.im.not.giving.you.the.flag@lol.com."
};

function getContent(fragmentId, callback){
  callback(pages[fragmentId]);
}

function loadContent(){
  var contentDiv = document.getElementById("app"),
      fragmentId = location.hash.substr(1);

  getContent(fragmentId, function (content) {
    contentDiv.innerHTML = content;
  });
}

var thing = atob(atob(atob("VERKR2EySlhiSFZNTUVaTVUydFNWRk5yV2t4U1JrNUxWRVZHVkZKcE9YSmpNbmhyWVcxYWRtRlhSbXRqTWxsMVpFaG9NQT09")));

fetch(thing).then(function(response) {
  return response.text();
}).then(function(data) {
  pages["secret"] = data;

  if(!location.hash) {
    location.hash = "#home";
  }
  loadContent();

  window.addEventListener("hashchange", loadContent)
});
```

Je remarque qu'il y a une variable contenant un triple encodage base64 : `var thing = atob(atob(atob("VERKR2EySlhiSFZNTUVaTVUydFNWRk5yV2t4U1JrNUxWRVZHVkZKcE9YSmpNbmhyWVcxYWRtRlhSbXRqTWxsMVpFaG9NQT09")));`

J'utilise l'outil decodify pour décoder cette string base64.

![image](https://user-images.githubusercontent.com/49941629/166079207-efc1ce1e-b601-4230-86e9-09d2a4188435.png)

A l'issue, je me rend sur `http://chall1.pctf.competitivecyber.club:10017/admin/AKJDSJFKDSJASF/ksldjfoiadsf.txt` 

Cela me renvois vers un lien pastebin qui contient le flag : https://pastebin.com/F21q9Eu8

FLAG : pctf{Th3_W3bsite_w@s_UnL0cK3d}
