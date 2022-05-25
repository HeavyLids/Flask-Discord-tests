### Установка
```sh
python3 -m pip install Flask-Discord
```


### Пример
```python
import os

from flask import Flask, redirect, url_for
from flask_discord import DiscordOAuth2Session, requires_authorization, Unauthorized

app = Flask(__name__)

app.secret_key = b"random bytes representing flask secret key"
os.environ["OAUTHLIB_INSECURE_TRANSPORT"] = "true"      # !! Only in development environment.

app.config["DISCORD_CLIENT_ID"] = 490732332240863233    # Discord client ID.
app.config["DISCORD_CLIENT_SECRET"] = ""                # Discord client secret.
app.config["DISCORD_REDIRECT_URI"] = ""                 # URL to your callback endpoint.
app.config["DISCORD_BOT_TOKEN"] = ""                    # Required to access BOT resources.

discord = DiscordOAuth2Session(app)


@app.route("/login/")
def login():
    return discord.create_session()
	

@app.route("/callback/")
def callback():
    discord.callback()
    return redirect(url_for(".me"))


@app.errorhandler(Unauthorized)
def redirect_unauthorized(e):
    return redirect(url_for("login"))

	
@app.route("/me/")
@requires_authorization
def me():
    user = discord.fetch_user()
    return f"""
    <html>
        <head>
            <title>{user.name}</title>
        </head>
        <body>
            <img src='{user.avatar_url}' />
        </body>
    </html>"""


if __name__ == "__main__":
    app.run()
```

For an example to the working application, check [`test_app.py`](tests/test_app.py)


### Требования
* Flask
* requests_oauthlib
* cachetools
* discord.py


### Документация
Head over to [documentation] for full API reference. 


[documentation]: https://flask-discord.readthedocs.io/en/latest/
