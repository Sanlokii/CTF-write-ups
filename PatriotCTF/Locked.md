![image](https://user-images.githubusercontent.com/49941629/166079395-a9049428-4c2a-4ee4-83d5-a6f3164156f8.png)


![image](https://user-images.githubusercontent.com/49941629/166079144-e2d4739c-8fd3-46b9-8675-7f05deb11730.png)


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

![image](https://user-images.githubusercontent.com/49941629/166079207-efc1ce1e-b601-4230-86e9-09d2a4188435.png)


https://pastebin.com/F21q9Eu8




pctf{Th3_W3bsite_w@s_UnL0cK3d}
