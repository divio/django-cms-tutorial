Step 5 - Installing A Blog App
==============================
We've already written our own django CMS plugins and apps, but now we want to extend our CMS with a third party app, [aldryn-blog](https://github.com/aldryn/aldryn-blog).

At first, we need to install the app from the Cheese Shop ([pypi.python.org](http://pypi.python.org)) (remember, always in the virtual environment!):

```bash
$ source env/bin/activate
(env) $ pip install aldryn-blog
```

Add the app and its requirements below to `INSTALLED_APPS` in `settings.py`:

```python
INSTALLED_APPS += (
    'aldryn_blog',
    'aldryn_common',
    'django_select2',
    'djangocms_text_ckeditor',
    'easy_thumbnails',
    'filer',
    'taggit',
)

One of the dependencies is ``easy_thumbnails``. It has already switched to Django 1.7 style migrations and needs some extra configuration to work with South:

```python
SOUTH_MIGRATION_MODULES = {
    'easy_thumbnails': 'easy_thumbnails.south_migrations',
}
```


```
Since we added a new app, we need to update our database:

```bash
(env) $ python manage.py syncdb
(env) $ python manage.py migrate
```

We can now run our server again

```bash
(env) $ python manage.py runserver
```

Run your server, add a new page for the blog and edit it. Go to ‘Advanced Settings’ and choose ‘Blog App’ for ‘Application’. This will hook the blog app into the page. For these changes to take effect, you will have to restart your server once again. If you reload your page now, you should be presented with the blog app!

Furthermore, check the toolbar. You can now select "Blog" > "Add Blog Post..." from it and add a new blog post directly from there (you should totally do that!)

Since the toolbar integration is totally awesome, we're going to integrate our poll app into the toolbar in [step 6](https://github.com/Chive/djangocms-tutorial/blob/master/Step%206%20-%20Toolbar%20Integration.md).
