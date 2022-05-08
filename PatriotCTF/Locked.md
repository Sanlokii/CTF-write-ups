# Patriot CTF

## Web

### Locked

**Difficulty:** Easy

**Statement:** My website has been completely secured! !! No matter how good a hacker you are, you will not be able to hack my site!!!

TODO: Put the URL of the website.

***

I start by fuzzing the site: `ffuf -w ~/fuzz-Bo0oM.txt -u http://chall1.pctf.competitivecyber.club:10017/FUZZ`

![image](https://user-images.githubusercontent.com/49941629/166079144-e2d4739c-8fd3-46b9-8675-7f05deb11730.png)

Thanks to the fuzzing, I know that there is a **/admin** directory.

So I inspect the source code of the **/admin/index.html** file and I notice a link to the **main.js** file.

Source code of **main.js** :

```js
var pages = {
  home: "Welcome to my completely unhackable site. There are no flags here, so don't even bother trying to look for them lol.",
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

var thing = atob(atob(atob("VERKR2EySlhiSFZNTUVaTVUydFNWRk5yV2t4U1JrNUxWRVZHVkZKcE9YSmpNbmhyWVcxYWRtRlhSbXRqTWxsMVpFaG9NQT09"));

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

I notice that there is a variable containing a triple base64 encoding: 

```
var thing = atob(atob("VERKR2EySlhiSFZNTUVaTVUydFNWRk5yV2t4U1JrNUxWRVZHVkZKcE9YSmpNbmhyWVcxYWRtRlhSbXRqTWxsMVpFaG9NQT09"));
```

I use the decodify tool to decode this base64 string.

![image](https://user-images.githubusercontent.com/49941629/166079207-efc1ce1e-b601-4230-86e9-09d2a4188435.png)

At the end, I go to `http://chall1.pctf.competitivecyber.club:10017/admin/AKJDSJFKDSJASF/ksldjfoiadsf.txt` 

This sends me to a pastebin link that contains the flag: https://pastebin.com/F21q9Eu8

Flag: pctf{Th3_W3bsite_w@s_UnL0cK3d}
