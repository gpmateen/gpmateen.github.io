---
layout: post
title:  "Django Tutorials"
date:   2020-06-20 11:02:31 -0500
categories: posts
---
This post covers the incremental additions to the [django-tutorials](https://github.com/gpmateen/django-tutorials) repository. Added a new application to the project called `polls` which is based off of [django tutorial](https://docs.djangoproject.com/en/3.0/intro/tutorial01/). In order to keep the UI interesting i'm using [materialize css](https://materializecss.com/) and its currently hosted on [pythonanywhere](http://gpmateen.pythonanywhere.com/).

If you want to run this project / application locally follow the instructions on the [django-tutorials](https://github.com/gpmateen/django-tutorials) repository [README](https://github.com/gpmateen/django-tutorials/blob/master/README.md) file.

If you want to read more about the tutorial itself, then head over to [django tutorial](https://docs.djangoproject.com/en/3.0/intro/tutorial01/).
<br>
<hr>
<br>
However in this post I wanted to highlight [`load.py`](https://github.com/gpmateen/django-tutorials/blob/master/load.py) which is created to load the initial data in the database, once the migration is complete. We run this script to load data into the database.

```python
import os
os.environ['DJANGO_SETTINGS_MODULE'] = 'django_tutorials.settings'

import django
django.setup()

from polls.models import *
import csv

from django.utils import timezone
import pytz

print('loading data into question/choice model')
with open('data/questions.csv') as f:
    reader = csv.reader(f)
    for row in reader:
        q = Question.objects.get_or_create(
            question_text=row[0], pub_date=timezone.now()
        )
        
        # load all the choices
        for choice in row[1:]:
            c = Choice.objects.get_or_create(
                question=q[0], 
                choice_text=choice, 
                votes=0
            )
```

The very first thing this script does, is to set environment variable `DJANGO_SETTINGS_MODULE` and call `django.setup()`, which gives us the window into application specific modules / utilities. 

```python
import os
os.environ['DJANGO_SETTINGS_MODULE'] = 'django_tutorials.settings'

import django
django.setup()
```

This is exactly what `python manage.py shell` command would do.

Next we import the models which needs to be loaded with initial data. 

```python
from polls.models import *
import csv
```

I would like to use `*` as supposed to using the name of the models specifically to relieve you of the frustration to add them in there when new models gets added. Next we read the data file (`csv`) which will get loaded into the models.

`data/questions.csv`

I've created a `csv` file for this process, let's read it and start creating objects.

```python
with open('data/questions.csv') as f:
    reader = csv.reader(f)
    for row in reader:
        q = Question.objects.get_or_create(
            row[0],
            timezone.now()
        )
```

Now let's load the appropriate choices for the given poll question, if you look at the `data/questions.csv`, you'll see that poll answers are place after the question itself. So let's slice the rows and read all the choices.

```python
for choice in row[1:]:
    c = Choice.objects.get_or_create(
        question=q[0], 
        choice_text=choice, 
        votes=0
    )
```

This gives us the flexibility of adding as many choices as you can, there could be as little as one or as many as 10 or more choices. With that if you head over to the application, you'll see some initial poll questions loaded as supposed to blank page. Also this saves us time to add the poll questions manually through django admin.

Thanks for following along, we'll see you in the next post.