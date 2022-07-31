# TFC CTF

## WEB

### DEEPLINKS

**Difficulty:** Easy

**Statement:** My intern configured my iOS app and my website to handle deeplinks, but they didn't tell me the path :( Can you help me find it?

***

The challenge announcement tells us about link and iOS.

Once on the website, it says **nothing to see here**. 

After some research, I find the following source: https://book.hacktricks.xyz/mobile-pentesting/ios-pentesting/ios-universal-links#retrieving-the-apple-app-site-association-file

> Try to retrieve the apple-app-site-association file from the server using the associated domains you got from the previous step. This file needs to be accessible via HTTPS, without any redirects, at https://<domain>/apple-app-site-association or https://<domain>/.well-known/apple-app-site-association.

So I add the path `/.well-known/apple-app-site-association` to the URL of the exposed web server: `http://01.linux.challenges.ctf.thefewchosen.com:50411/.well-known/apple-app-site-association`

We get the file `apple-app-site-association` with the following content:  

```
{
    "applinks": {
        "apps": [],
        "details": [
            {
                "appID": "ABCDEFGHIJ.com.example.example",
                "paths": ["TFCCTF{4ppl3_4pp_51t3_4550c14t10n}"]
            }
        ]
    }
}
```

Flag: `TFCCTF{4ppl3_4pp_51t3_4550c14t10n}`
