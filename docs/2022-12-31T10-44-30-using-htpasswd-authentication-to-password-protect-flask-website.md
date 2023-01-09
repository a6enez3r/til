---
layout: default
date: 2022-12-31T10:44:30-05:00
title: Using Htpasswd Authentication To Password Protect Flask Website
tags: 
---

# Using Htpasswd Authentication To Password Protect Flask Website

- install `htpasswd` tool

```bash
    npm install -g htpasswd
```

- generate auth file

```bash
    htpasswd -c /path/to/.htpasswd username
```

`NOTE`: the file above doesn't need to have a `.htpasswd` extension

- you can update or reset the password using

```bash
    htpasswd /path/to/.htpasswd username
```

- once you have the auth file setup, configure your Flask application

```python
import flask
from flask_htpasswd import HtPasswdAuth

app = flask.Flask(__name__)
app.config['FLASK_HTPASSWD_PATH'] = '/path/to/.htpasswd'
app.config['FLASK_SECRET'] = 'Hey Hey Kids, secure me!'

htpasswd = HtPasswdAuth(app)


@app.route('/')
@htpasswd.required
def index(user):
    return 'Hello {user}'.format(user=user)

app.run(debug=True)
```

`NOTE`: use `app.config['FLASK_AUTH_ALL']=True` to password protect all views and don't want to use `@htpasswd.required`