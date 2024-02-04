# learning-flask

### 04 February, 2024
# Flask Mega Tutorial - Learning Summary

## Introduction

Welcome to the world of web development with **Flask!** 

This README summarizes the key concepts I've learned from the [Flask Mega Tutorial](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world), covering three fundamental topics: "Hello World," "Web Forms," and "Templates." 

## Table of Contents
1. [Hello World](#hello-world)
2. [Templates](#templates)
3. [Web Forms](#web-forms)

## Hello World

In the first part of the tutorial, I got started with Flask by building a simple "Hello World" application. Here are the key takeaways:


- **Creating Virtual Environment:** 
	Here the first `venv` is for the command for the `virtualbox` and the second `samisvenv` is a name we saved for the virtual environment
	
```bash
$ python3 -m venv samisvenv
```

- **Activate the virtual environment:** 
```bash
$ source samisvenv/bin/activate
(samisvenv) $ _
```
	
- **Flask Installation:** I learned how to install Flask using `pip` and set up a virtual environment to manage dependencies.

```bash
$ pip install flask
```

- **Creating the App:** 
The photo from blog I followed:

![App creating](https://i.ibb.co/qj3J7Vt/Pasted-image.png)

- **Folder design:**   I followed this pattern to create folders.

```
  microblog/
  venv/
  app/
    __init__.py
    routes.py
  microblog.py

```

- **Deployment:**  To deploy/run the code I followed these code from the blog.

![alt text](https://i.ibb.co/vmKHGWG/Screenshot-from-2024-02-04-23-59-13.png)

## Templates
The second part focused on templates(It's like we make components in react/pages for routing), allowing to separate logic and presentation:

- **Dynamic Content:** Passing dynamic data from the Python code to the HTML templates using the `render_template` function.

## Web Forms
In the third part, you explored the world of web forms, gaining insights into handling user input:

- **Form Creation:** Building a simple web form using the `FlaskForm` class and form fields. Learned to create a form for user input.

- **Form Rendering:** Displaying the form in an HTML template and handling form submissions.

- **Flash Messages:** Using `flash` to provide feedback to users after form submission.

## **Code base & Folder Pattern:**
#### Folder Pattern:
```
 microblog/
  svenv/
  app/
    __init__.py
    routes.py
  config.py
  microblog.py
```


#### Code base:

**microblog.py:
```python
from app import app
```

**config.py:**
```python
import os
basedir = os.path.abspath(os.path.dirname(__file__))
class Config:

SECRET_KEY = os.environ.get('SECRET_KEY') or 'you-will-never-guess'

SQLALCHEMY_DATABASE_URI = os.environ.get('DATABASE_URL') or \

'sqlite:///' + os.path.join(basedir, 'app.db')
```
**app/__init__.py:**
```python
from flask import Flask
from config import Config
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate

app = Flask(__name__)

app.config.from_object(Config)

db = SQLAlchemy(app)

migrate = Migrate(app, db)

from app import routes, models
```
**app/forms.py:**
```python
from flask_wtf import FlaskForm
from wtforms import StringField, PasswordField, BooleanField, SubmitField
from wtforms.validators import DataRequired

class LoginForm(FlaskForm):

username = StringField('Username', validators=[DataRequired()])

password = PasswordField('Password', validators=[DataRequired()])

remember_me = BooleanField('Remember Me')

submit = SubmitField('Sign In')
```
**app/routes.py:**
```python
from app import app
from flask import render_template, flash, redirect, url_for
from app.forms import LoginForm

@app.route('/')
@app.route('/index')
def index():
	user = {'username': 'Samii'}
	posts = [
	{
	'author': {'username': 'John'},
	'body': 'Beautiful day in Portland!'
	},
	{
	'author': {'username': 'Susan'},
	'body': 'The Avengers movie was so cool!'
	}
	]
return render_template('index.html', title = 'Home - ', user = user, posts =     posts)

@app.route('/login', methods = ['GET', 'POST'])
def login():
form =LoginForm()
if form.validate_on_submit():
	flash('Login requested for user {}, remember_me={}'.format(
	form.username.data, form.remember_me.data))
	return redirect(url_for('index'))

return render_template('login.html', title = 'Sign In', form = form)
```
**templates/base.html:**
```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Document</title>
</head>
<body>
<hr>
<div>
Microblog:
<a href="{{ url_for('index')}}">Home</a>
<a href="{{ url_for('login')}}">Login</a>
</div>
<hr>
{% with messages = get_flashed_messages() %}
{% if messages %}
<ul>
{% for message in messages %}
<li>{{ message }}</li>
{% endfor %}
</ul>
{% endif %}
{% endwith %}
{% block content %}{% endblock %}
</body>
</html>
```
**templates/index.html:**
```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
{% if title %}
<title>{{ title }} - Microblog</title>
{% else %}
<title>Welcome to Micro-blog</title>
{% endif %}
</head>
<body>
{% extends "base.html" %}
{% block content %}
<h1>Hello, {{ user.username }} !</h1>
{% for post in posts %}
<div><p>{{ post.author.username }} says: <b>{{ post.body }}</b></p></div>
{% endfor %}
{% endblock %}
</body>
</html>
```
**templates/login.html:**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{ title }}</title>
</head>
<body>
    {% extends "base.html" %}
    {% block content %}
    <h1>Sign In</h1>
    <form action="" method="post" novalidate>
        {{ form.hidden_tag() }}
        <p>
            {{ form.username.label }}<br>
            {{ form.username(size=32) }}
            {% for error in form.username.errors %}
                <span style="color: red;">[{{ error }}]</span>
            {% endfor %}
        </p>
        <p>
            {{ form.password.label }}<br>
            {{ form.password(size=32) }}
            {% for error in form.password.errors %}
                <span style="color: red;">[{{ error }}]</span>
            {% endfor %}
        </p>
        <p>{{ form.remember_me() }} {{ form.remember_me.label }}</p>
        <p>{{ form.submit() }}</p>
    </form>
{% endblock %}
</body>
</html>
```

### Some extra line:
There are some extra file or code for the next practice part which I didn't have completed yet in some file.

## Next Steps

As move forward, consider exploring additional Flask extensions, databases integration, and building more complex features to enhance my flask web development skills.

