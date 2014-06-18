Step 3 - CMS Plugins
====================

In this part of the tutorial we're going to take the django poll app and modify it in a way to use it in the CMS.


Install the polls app
---------------------

``pip install -e git+ssh://git@github.com/divio/django-polls.git#egg=django-polls`` to get a version of <https://docs.djangoproject.com/en/dev/intro/tutorial01/>. This app does not have any django-cms integration yet.

You should end up with a folder structure similar to this:

```
demo/
    env/
        src/
            django-polls/
                polls/
                    __init__.py
                    admin.py
                    models.py
                    templates/
                    tests.py
                    urls.py
                    views.py
    my_site/
    project.db
    requirements.txt
```

Let's add it to our project. Add your polls plugin to the `INSTALLED_APPS` in your projects `settings.py`:

```python
INSTALLED_APPS += ('polls', )
```

Add the following line to the project's `urls.py`:

```python
url(r'^polls/', include('polls.urls', namespace='polls')),
```

Make sure this line before the line for the django-cms urls, or else django-cms will try to interperet the polls url as a slug, fail, and return a 404.

```python
url(r'^polls/', include('polls.urls', namespace='polls')),
url(r'^', include('cms.urls')),
```

Now run the migrations using south.

```bash
(env) $ python manage.py migrate polls
```

At this point you should be able to add polls in admin and fill them in at `/polls/`.

But we've lost our page navigation. Let's restore it by overriding the polls base template.

add ``my_site/templates/polls/base.html``:

```python
{% extends 'base.html' %}

{% block content %}
    {% block polls_content %}
    {% endblock %}
{% endblock %}
```

Open the polls views again. The navigation should be visible now.

So now we have integrated the standard polls app in our project. But we've not done anything django CMS specific yet. It's time to integrate!


Our first plugin
----------------

If you've played around with the CMS for a little, you've probably already encountered CMS Plugins. They are the objects you can fill into placeholders on your pages through the frontend (e.g. "Text", "Image" and so forth).

We're now going to extend the django poll app so we can embed a poll easily into any CMS page. We'll put this code in a separate package in our project. This allows integrating 3rd party apps without having to fork them. It would also be possible to add this code directly into the django-polls app to make it integrate out of the box.

Create a new package at the project root called ``djangocms_polls``.

### The Plugin Model

In your poll application’s `models.py` add the following:

```python
from cms.models import CMSPlugin
from django.db import models
from polls.models import Poll

class PollPlugin(CMSPlugin):
    poll = models.ForeignKey(Poll)

    def __unicode__(self):
      return self.poll.question
```

> **Note:** django CMS plugin models must inherit from `cms.models.CMSPlugin` (or a subclass thereof) and not `models.Model`.


### The Plugin Class
Now create a file `cms_plugins.py` in the same folder your models.py is in. The plugin class is responsible for providing django CMS with the necessary information to render your plugin.

For our poll plugin, we're going to write the following plugin class:

```python
from cms.plugin_base import CMSPluginBase
from cms.plugin_pool import plugin_pool
from djangocms_polls.models import PollPlugin
from django.utils.translation import ugettext as _


class CMSPollPlugin(CMSPluginBase):
    model = PollPlugin  # Model where data about this plugin is saved
    module = _("Polls")
    name = _("Poll Plugin")  # Name of the plugin
    render_template = "djangocms_polls/poll_plugin.html"  # template to render the plugin with

    def render(self, context, instance, placeholder):
        context.update({'instance': instance})
        return context

plugin_pool.register_plugin(CMSPollPlugin)  # register the plugin
```

> **Note**: All plugin classes must inherit from `cms.plugin_base.CMSPluginBase` and must register themselves with the `cms.plugin_pool.plugin_pool`.

> **Note:** Don't name the plugin model and the plugin admin the same. Otherwise things get confusing.


### The Template
You probably noticed the render_template attribute in the above plugin class. In order for our plugin to work, we need to set it up first.

The template is located at `djangocms_polls/templates/djangocms_polls/poll_plugin.html` and should look something like this:

```html
<h1>{{ instance.poll.question }}</h1>

<form action="{% url 'polls:vote' instance.poll.id %}" method="post">
    {% csrf_token %}
    {% for choice in instance.poll.choice_set.all %}
        <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}" />
        <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br />
    {% endfor %}
    <input type="submit" value="Vote" />
</form>
```

Now add ``djangocms_polls`` to ``INSTALLED_APPS`` and create a database migration to add the plugin table (using South):

```bash
(env) $ python manage.py schemamigration djangocms_polls --init
(env) $ python manage.py migrate djangocms_polls
```

Finally, run the server and go visit <http://localhost:8000/>. You can now add the ``Poll Plugin`` anywhere you want to display a poll. Yay!


And add the Polls CMS Plugin on a page.


Next up is step 4: [CMS Apps](https://github.com/Chive/djangocms-tutorial/blob/master/Step%204%20-%20CMS%20Apps.md)
