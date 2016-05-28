
## Production Ready Flask App

Notes from Miguel Grinberg's talk on making a production flask app.

PyCon 2016 - 20160528 0900 PST


### 1. Move utility functions to utils.py (tag: v0.2)

url_for, timestamp functions.


### 2. Refactor database models. (tag: v0.3)

Avoid cyclic dependency of importing db from controller then importing model from models.py

Add a check for __main__ in models.py, if there isn't a main then fallback to ugly hardcoded "from flack import db".


### 3. Create an Application Package (tag: v0.4) - Use Flask-Script

A newer option exists over Flask-Script but is not yet production ready.

Flask-Script is available now: 
https://flask-script.readthedocs.io/en/latest/

See `git diff v0.3 v0.4` between these two tags to understand necessary changes.


### 4. Refactoring API Authentication (tag: v0.5) - 

Moved auth stuff to auth.py.  There were three auth functions finally imported.

Again see `git diff v0.4 v0.5` for details.


### 5. Refactoring Tests (tag: v0.6) - Change to a package style folder structure.

To get the overview, checkout this tag and manage.py. Look at file structure.

Now we are in a django style construction. `tests` folder exists alongside `flack` folder.


### 6. Refactoring Configuration (tag: v0.7) - Add a config.py next to manage.py.

Add a config.py. This is pretty hairy even in django.

People solve configuration in different ways. 

I have a pretty good django dev.cfg/prod.cfg pattern that I would use here.


### 7. Create an `API Blueprint` - separate the api.

Create: `flack/api.py`.  Use a Blueprint.  Blueprint is initialized where app creation is done, currently flack.py.


### 8. Create Stats package.

See `git diff v0.8 v0.9`


### 9. Using an application Factory Function (tag: v0.10) - use a Blueprint for app.

>>> Sometimes it is desirable to work with more than one application
>>> Best Example: Unit tests that need applications with different configurations.

We need to get rid of app because it will exist outside the context of the controller.

Now we can no longer reference app.  
We must refactor app.config and other references to app.
That means no app.debug.

To achieve this we can use a Blueprint.
Use the current_app context variable to access application.

Put application factory function in __init__.py, see create_app function here.

To create an app you now call create_app, pass an application name, and get the app.

SQLAlchemy needs the models in __init__.py because it introspects on the app.
You can miss this and maybe the models get imported elsewhere but that is sloppy.



