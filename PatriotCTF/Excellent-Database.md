# Patriot CTF

## Web

### Excellent Database

**Difficulté :** Facile

**Enoncé :** Je suis en train de créer la prochaine grande plateforme de réseaux sociaux, mais j'ai l'habitude de mettre en place des logiciels non sécurisés.

Si vous pouvez vous connecter à l'utilisateur admin, je vous donnerai le flag.

Je teste cette nouvelle architecture de base de données et elle est si belle que je vous laisserai regarder le code.

Mise à disposition du code source **main.py** :

```python
import os

from flask import render_template, redirect, request, session, flash, url_for
from openpyxl import load_workbook
import subprocess
import pandas as pd
from app import app, USERNAMES, PASSWORDS


def add_user(username, password):
    DB = load_workbook(filename="db.xlsx")
    Users = DB["Users"]
    new_row = Users.max_row + 1
    Users[f"{USERNAMES}{new_row}"] = username
    Users[f"{PASSWORDS}{new_row}"] = password
    DB.save(filename="db.xlsx")

def read_db() -> pd.DataFrame:
    subprocess.Popen(["libreoffice", "--headless", "--convert-to", "csv", "db.xlsx"], stdout=subprocess.DEVNULL, stderr=subprocess.STDOUT).communicate()
    df = pd.read_csv("db.csv")
    return df


@app.route("/", methods=["POST", "GET"])
def base():
    if not session.get("username"):
        return redirect(url_for("login"))
    else:
        Users = read_db()
        username = session.get("username")
        password = Users.query(f"Username == '{username}'")["Password"].values[0]
        return render_template('index.html', name=username, password=password)


@app.route("/admin", methods=["GET"])
def admin():
    username = session.get("username")
    if username != "admin":
        return redirect("/")
    else:
        return render_template("admin.html", name=username)


@app.route('/login', methods=['GET','POST'])
def login():
    if request.method == "GET":
        return render_template("login.html")
    else:
        username = request.form.get("username")
        password = request.form.get("password")

        Users = read_db()
        
        if username not in Users.Username.values:     
            flash('Please check your login details and try again.', 'danger')
            return redirect(url_for('login'))
        elif password != Users.query(f"Username == '{username}'")["Password"].values[0]:
            flash('Please check your login details and try again.', 'danger')
            return redirect(url_for('login'))

        session["username"] = request.form.get("username")
        return redirect("/")


@app.route('/signup', methods=['GET','POST'])
def signup():
    if request.method=='POST':
        username = request.form.get("username")
        password = request.form.get("password")

        Users = read_db()
        if username in Users.Username.values:     
            flash('Username already exists')
            return redirect(url_for('signup'))
        else:
            add_user(username, password)
            session["username"] = username
            return redirect("/")
    else:
        return render_template('signup.html')


@app.route('/logout', methods=['GET'])
def logout():
    if request.method=='GET':
        username = session.get("username")
        session.pop('username', default=None)
        return redirect("/")


if __name__ == "__main__":
    for f in os.listdir("flask_session"):
        os.remove(os.path.join("flask_session", f))

    app.run(debug=False, host='0.0.0.0', threaded=True)
```

***

En lisant le code source, on peut s'apercevoir que la base de données utilisée est le fichier **db.xlsx**.

Il va donc falloir bypass l'authentification via une CSVi (CSV Injection).

Lors de la création d'un compte, on peut constater que la page web affiche notre mot de passe lorsque l'on est authentifié.

![image](https://user-images.githubusercontent.com/49941629/166080611-b551ae13-06e5-4ad4-90cf-79bed588f930.png)

Le but va être d'injecter notre payload sur le champ password afin de récupérer le password du compte admin.

Je me connecte avec mon compte et j'utilise le payload suivant dans le champ password : `=CONCATENATE(A1:B1)`

Une fois authentifié, le site m'indique que mon password est `UsernamePassword`

Cela ressemble fortement à des en-têtes de colonnes, j'essaye donc de me m'authentifier avec le payload `=CONCATENATE(A2:B2)`

Je récupère le password du compte admin :
![image](https://user-images.githubusercontent.com/49941629/166080684-7400f832-64c2-49d3-b092-9b44f15eebf9.png)

Il me reste plus qu'à m'authentifier avec les credentials `admin:SuperStrongPassword` pour récupérer le flag :

![image](https://user-images.githubusercontent.com/49941629/166080726-271a85c0-8132-4de2-9d19-0c7dd2468754.png)

FLAG : PCTF{Exc3l_is_th3_b3st_d4t4b4s3}
