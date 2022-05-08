# CTF Midnight Flag : INFEKTION

## Steganography

### Nothing is classified

**Difficulty:** Easy

***

The challenge starts with this image:

![image](https://user-images.githubusercontent.com/49941629/165689108-f76cdf3c-f460-4e4a-9b76-64ad15b2d16e.png)

By modifying the saturation and the chroma hue of the image, we can see a string in base64 :

![image](https://user-images.githubusercontent.com/49941629/165690706-ba35a4fd-56a7-48c7-a78e-2d91016edeb9.png)

I then decode the base64 string:

```
echo -n "YW50aW1pc3NpbGVkZWZlbnNl" | base64 --decode
```

With this command, I get in output the value **antimissiledefense**.

This value does not correspond to the flag, so I suppose that it will be used to retrieve the real information.

I decide to use the steghide tool which allows to hide or extract data in image or audio files.

So I run the following command using the passphrase during the interactive mode: **antimissiledefense**

```
steghide extract -sf classified.jpeg
```

The tool extracts the data and generates the file **flag.txt**.

All I have to do is display the contents of the file to retrieve the flag:

```
cat flag.txt
```

Flag : MCTF{cL@$Sif1eD_D0cUm3N7}
