# Patriot CTF

## Web

### Excellent Database

**Difficulty:** Easy

**Statement:** I am creating the next big social networking platform, but I am used to setting up unsecured software.

If you can login to the admin user, I will give you the flag.

I'm testing this new database architecture and it's so beautiful I'll let you look at the code.

Making the source code available **main.py** :

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
        password = Users.query(f "Username == '{username}'")["Password"].values[0]
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
        elif password != Users.query(f "Username == '{username}'")["Password"].values[0]:
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

Reading the source code, we can see that the database used is the file **db.xlsx**.

We will have to bypass the authentication via a CSVi (CSV Injection).

When creating an account, we can see that the web page displays our password when we are authenticated.

![image](https://user-images.githubusercontent.com/49941629/166080611-b551ae13-06e5-4ad4-90cf-79bed588f930.png)

The goal is to inject our payload on the password field in order to get the password of the admin account.

I connect with my account and I use the following payload in the password field: `=CONCATENATE(A1:B1)`

Once authenticated, the site tells me that my password is `UsernamePassword`.

This looks very much like column headers, so I try to authenticate with the payload `=CONCATENATE(A2:B2)`

I get the password for the admin account:
![image](https://user-images.githubusercontent.com/49941629/166080684-7400f832-64c2-49d3-b092-9b44f15eebf9.png)

I just have to authenticate with the credentials `admin:SuperStrongPassword` to get the flag:

![image](https://user-images.githubusercontent.com/49941629/166080726-271a85c0-8132-4de2-9d19-0c7dd2468754.png)

Flag: PCTF{Exc3l_is_th3_b3st_d4t4b4s3}
