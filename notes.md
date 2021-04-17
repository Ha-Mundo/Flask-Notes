# Flask Cheat Sheet

## 1 - What is Flask

**Flask** is a lightweight **WSGI** web application framework. It is designed to make getting started quick and easy, with the ability to scale up to complex applications. Classified as a microframework, Flask aims to keep the core of its framework small but highly extensible.

---

## 2 - Install Flask

    $ pip install Flask

---

## 3 - Creating an isolated environment

    python -m venv flask_env

Activate virtual environment

    source source_env/bin/activate

Deactivate virtual environment

    deactivate

---

## 4 - A super simple Flask app

    from flask import Flask

    app = Flask(__name__)

    @app.route('/')
    def hello():
        return f'Hello from Flask!'

---

## 5 - Start the app

    $ env FLASK_APP=hello.py flask run

    * Serving Flask app "hello"
    * Running on http://127.0.0.1:5000/

---

## 6 - Object-Based Configuration

Create a new config file in the project root:

    `PROJECT_ROOT/app/config.py`

Add content using OOP and class inheritance

    class BaseCfg(object):
        SECRET_KEY = 'SuperS3cretKEY_1222'
        DEBUG = False
        TESTING = False

    class DevelopmentCfg(BaseCfg):
        DEBUG = True
        TESTING = True

    class TestingCfg(BaseCfg):
        DEBUG = False
        TESTING = True

    class ProductionCfg(BaseCfg):
        SECRET_KEY = 'HvFDDcfsnd\_\_9nbCdgsada'
        DEBUG = False
        TESTING = False

Let's use the configuration:

Update `PROJECT_ROOT/app/__init__.py` to use it

    from flask import Flask
    from app import config

    app = Flask(__name__)
    app.config.from_object(config.DevelopmentCfg) # <--- Magic line

---

## 7 - Render page using Jinja

The Flask controller

    from flask import Flask, render_template
    app = Flask(**name**)

    @app.route("/test")
    def template_test():
        return render_template('template.html', my_string="Jinja Works!!")

    if __name__ == '__main__':
        app.run(debug=True)

The HTML template

    <html>
      <head>
        <title>Flask Template Example</title>
      </head>
      <body>
        <div class="container">
          <p>Result: {{my_string}}</p>
        </div>
      </body>
    </html>

By accessing `http://localhost:5000/test` in the browser, we should see:

Status: Jinja Works

---

## 8 - Using Flask shell

    $ flask shell

---

## 9 - Define a model with SQLAlchemy using SQLite

Create `PROJECT_ROOT/app/models.py` with following content:

    from flask_sqlalchemy import SQLAlchemy

    db = SQLAlchemy()

    class User(db.Model):

        id       = db.Column(db.Integer,     primary_key=True)
        user     = db.Column(db.String(64),  unique = True)
        email    = db.Column(db.String(120), unique = True)
        password = db.Column(db.String(500))

        def __init__(self, user, email, password):
            self.user       = user
            self.password   = password
            self.email      = email

Update `PROJECT_ROOT/app/` **config.py** with the Database connection string

    class BaseCfg(object):
        SECRET_KEY = 'SuperS3cretKEY_1222'
        DEBUG = False
        TESTING = False

        # This will create a file in <app> FOLDER
        SQLALCHEMY_DATABASE_URI = 'sqlite:///' + os.path.join(basedir,'db. sqlite3')
        SQLALCHEMY_TRACK_MODIFICATIONS = False

Update `PROJECT_ROOT/app/__init__.py` to use the model

    from flask_sqlalchemy import SQLAlchemy
    ...

    db = SQLAlchemy (app) # flask-sqlalchemy

The `User` model can be created using flask shell

    $ flask shell

    > > from app import db
    > > db.create_all()

The second method is to create the tables automatically at first request using a hook provided by Flask.

Update `PROJECT_ROOT/app/__init__.py` as below:

    # Setup database

    @app.before_first_request
    def initialize_database():
        db.create_all()

---

## 10 - List users via Flask shell

    $ flask shell

    > > > from app.models import User
    > > > User.query.all()

    # HERE we should see all users

---

## 11 - Flask Database Migration

A migration means when we need to add, remove or update the type of a database table column. To safely reflect our update on the database, we need to "migrate" the DB schema.

Used module: `Flask-Migrate`

Install Flask-Migrate

    $ pip install Flask-Migrate

Update `PROJECT_ROOT/app/__init__.py` as below:

    ...
    app = Flask(__name__)
    ...
    migrate = Migrate(app, db) # <- The magic line

If we execute a migration for the first time:

    $ # This will create the migrations folder
    $ flask db init

**Migrate the schema** - this will generate the SQL (no database change)

    $ flask db migrate

**Update the Database** - using the SQL generated in the previous step

    $ flask db upgrade

---
