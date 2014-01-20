django CMS Tutorial - Step 1
============================
Good to see you! Now let's get started by setting up our environment.

Preparing your workstation
--------------------------
In order to keep an overview about view packages are installed, we create a virtual environment for every project. Make sure you have the latest version installed:

```bash
$ pip install --upgrade virtualenv
```

Installing the CMS
------------------

### Make a project folder

Go back to your workspace and create a new folder for this project:

```bash
$ cd ~/workspace    
$ mkdir demo && cd demo
```

### Setup virtual environment

Let's set up the virtual environment and install [`aldryn-installer`](https://github.com/nephila/aldryn-installer):

```bash
$ virtualenv env --no-site-packages
$ source env/bin/activate
(env) $ pip install "git+https://github.com/nephila/aldryn-installer#egg=aldryn-installer"
```

***Note:*** If you're using Windows, activate the virtual environment by doing this instead:

```
> env\Scripts\activate
```

### Install django CMS

We're now about to install django CMS. We'll do that using previously installed `aldryn-installer` since it's easy and hassle-free. Just follow the interactive setup, don't worry if it takes a while :)

```
(env) $ aldryn -p . my_demo
```

***Note:*** Again, if you're using Windows you'll have to make sure your python files are associated correctly. Open up a shell with admin rights:

```
C:\Windows\system32> assoc .py=Python.file
.py=Python.file

C:\Windows\system32> ftype Python.File="C:\Users\Username\workspace\demo\env\Scripts\python.exe" "%1" %*
Python.File="C:\Users\Username\workspace\demo\env\Scripts\python.exe" "%1" %*
```

If you want to be safe, use the settings below:

```bash
$ Database configuration (in URL format) [default sqlite://locahost/project.db]: [ENTER]
$ django CMS version (choices: 2.4, stable, beta, develop) [default stable]: beta
$ Django version (choices: 1.4, 1.5, 1.6, stable) [default 1.5]: [ENTER]
$ Activate Django I18N / L10N setting (choices: yes, no) [default yes]: [ENTER]
$ Install and configure reversion support (choices: yes, no) [default yes]: [ENTER]
$ Languages to enable. Option can be provided multiple times, or as a comma separated list: en,de
$ Optional default time zone [default America/Chicago]: Europe/Zurich
$ Activate Django timezone support (choices: yes, no) [default yes]: [ENTER]
$ Activate CMS permission management (choices: yes, no) [default yes]: [ENTER]

Creating admin user
$ Username: admin
$ Email address: email@example.com
$ Password: admin
$ Password (again): admin
```

### Create a template
The installer did most of the work for us, however we need to add our custom templates to the CMS. First, delete the two existing files in `demo/my_demo/templates/`. Afterwards, create these two files in that folder:

**`base.html`**:

```html
{% load cms_tags sekizai_tags menu_tags %}
<html>
    <head>
        {% render_block "css" %}
    </head>
    <body>
        {% cms_toolbar %}
        <ul>
            {% show_menu 0 100 100 100 %}
        </ul>
        {% block content %}{% endblock %}
        {% render_block "js" %}
    </body>
</html>
```

**`page.html`**:

```html
{% extends "base.html" %}
{% load cms_tags sekizai_tags menu_tags placeholder_tags %}

{% block content %}
    {% placeholder content %}
{% endblock %}
```

Next, open up `my_demo/settings.py`, look for `CMS_TEMPLATES` and include our newly created `page.html` template:

```html
CMS_TEMPLATES = (
    ('page.html', 'Page'),
)
```

### Let's run it

Start the server:

```bash
(env) $ python manage.py runserver
```

Now you can open [http://127.0.0.1:8000/](http://127.0.0.1:8000/) in your browser and should see the django CMS welcome screen.

Congratulations, you now have a fully functional CMS! Awesome job!

Let's login and add some content:

* click on the [Switch to edit mode](http://localhost:8000/?edit) and login with the user you created earlier.
* click on the add page link. Enter "home" as title of your new page and save.
* workaround: open "example.com" -> "Pages …" and click on the publish icon of your newly created page.
* switch to the `structure` mode and start adding plugins. A good one to start with is the "Text"-Plugin.


Let's continue with [Step 2 - Placeholder.md](https://github.com/Chive/djangocms-tutorial/blob/new/Step%201%20-%20Placeholder.md)!
