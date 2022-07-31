# TFC CTF

## WEB

### CALENDAR

**Difficulty:** Medium

**Statement:** Are online calendars trusty?
Note: The website displays "Error establishing a database connection" at startup. Please wait ~10 seconds and refresh
The flag is format :TFCCTF{FOUNDPASSWORD}

***

Once on the website, we can see that it is managed by the CMS WordPress :

![image](https://user-images.githubusercontent.com/49941629/182033006-578f841c-7df4-43cb-ac5b-fb45c8199282.png)

After browsing the website, I find the search page via the parameter **s** in GET request : `http://01.linux.challenges.ctf.thefewchosen.com:49389/?s=calendar`

![image](https://user-images.githubusercontent.com/49941629/182033243-47179f26-81e1-45d0-8219-136ff54e7ec5.png)

A simple search with the keyword **admin** gives us the following result:

![image](https://user-images.githubusercontent.com/49941629/182033310-7af2d87a-d767-439d-a0f4-02a8d9c48c30.png)

With this information, we can connect to the administration part of WordPress `/wp-admin`.

![image](https://user-images.githubusercontent.com/49941629/182033416-ebe70fbf-423f-4597-b72d-c7a4f814cf95.png)

Flag: `TFCCTF{WPNe3MgF$sNj8E8F6d}`
