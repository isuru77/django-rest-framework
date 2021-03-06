## start project
>> django-admin startproject mysite

### Run on custom port
>> python manage.py runserver 8080

### startapp and startproject is different - project is web of multiple apps etc.
### start app
>> python manage.py startapp polls

### You should always use include() when you include other URL patterns. admin.site.urls is the only exception to this.
urlpatterns = [
    path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
]

# DATABASE
ENGINE – Either 'django.db.backends.sqlite3', 'django.db.backends.postgresql', 'django.db.backends.mysql', or 'django.db.backends.oracle'. Other backends are also available.

NAME – The name of your database. NAME should be the full absolute path ==> os.path.join(BASE_DIR, 'db.sqlite3')

## To setup database
>> python manage.py migrate

## to test open sqllite client
>> sqlite3 db.sqlite3
### and tun
>> .schema 
### will give all  tables created

The default applications are included for the common case, but not everybody needs them. If you don’t need any or all of them, feel free to comment-out or delete the appropriate line(s) from INSTALLED_APPS before running migrate. 


# MAKEING MODELS (polls/modles.py)
==================================================================
    from django.db import models

    class Question(models.Model):
        question_text = models.CharField(max_length=200)
        pub_date = models.DateTimeField('date published')


    class Choice(models.Model):
        question = models.ForeignKey(Question, on_delete=models.CASCADE)
        choice_text = models.CharField(max_length=200)
        votes = models.IntegerField(default=0)
=====================================================================
### Each field is represented by an instance of a Field class 
### – e.g:  CharField     for character fields and 
###         DateTimeField for datetimes. 

###name of each Field instance (e.g. question_text or pub_date) is the field’s name
###-database will use it as the column name.

### If this field isn’t provided, Django will use the machine-readable name
### ex:  question = models.ForeignKey(Question, on_delete=models.CASCADE)
### field name will be 'question' !!! Awesom right!!!


# INSTALLING POLLS APP
After creating models, add polls app to INSTALLED_APPS(pollapp/settings.py)

INSTALLED_APPS = [
    'polls.apps.PollsConfig', # <----------------------------------------------- installing(adding) polls app
    'django.contrib.admin', # The admin site
    ...


# CRAETING DATABASE SCHEMAS FOR APP
>> python manage.py makemigrations polls
###By running makemigrations, you’re telling Django that you’ve made some changes to your models (in this case, you’ve made new ones) and that you’d like the changes to be stored as a migration.
#NOTE: Migrations are how Django stores changes to your models (and thus your database schema)

# CONVERT MIGRATION TO SQL COMMAND
>> python manage.py sqlmigrate polls 0001
#The sqlmigrate command takes migration names and returns their SQL

## NOTES:
1. Table names are automatically generated by combining the name of the app (polls) and the lowercase name of the model – question 
    - polls_choice (You can override this behavior.)
2. Primary keys (IDs) are added automatically. (You can override this, too.)
3.  appends "_id" to the foreign key field name(You can override this, too.)
4. It’s tailored to the database you’re using, so database-specific field types such as auto_increment (MySQL), serial (PostgreSQL), or integer primary key autoincrement (SQLite) are handled for you automatically. Same goes for the quoting of field names – e.g., using double quotes or single quotes.

## NOTE:: The sqlmigrate command doesn’t actually run the migration on your database - 
### it just prints it to the screen so that you can CHECK, change if need.

### you can also run this to check any problems in your project without making migrations or touching the database.
>> python manage.py check
> System check identified no issues (0 silenced).

# RUNNING MIGRATION - ADDING TABLES
>> python manage.py migrate


## Django tracks which ones are applied using a special 
## table in your database called "django_migrations"


# CHANGING MODELS STEPS
    1. Change your models (in models.py).
    2. Run python manage.py makemigrations to create migrations for those changes
    3. Run python manage.py migrate to apply those changes to the database.


# DJANGO SHELL TO PLAY
>> python manage.py shell
### You can see tables, query and play with app using shell as follows
    >>> from polls.models import Choice, Question  # Import the model classes we just wrote.
    # No questions are in the system yet.
    >>> Question.objects.all()
    <QuerySet []>

    >>> from django.utils import timezone
    >>> q = Question(question_text="What's new?", pub_date=timezone.now())

    MORE: https://docs.djangoproject.com/en/2.2/intro/tutorial02/#playing-with-the-api

#### It’s important to add __str__() methods to your models, not only for your own convenience when dealing with the interactive prompt, but also because objects’ representations are used throughout Django’s automatically-generated admin.

### Handling time zones: https://docs.djangoproject.com/en/2.2/topics/i18n/timezones/

### Django queries:
```
>>> from django.utils import timezone
>>> q = Question(question_text="What's new?", pub_date=timezone.now())
>>> q.save()
>>> q.pub_date
>>> Question.objects.all()

>>> current_year = timezone.now().year
>>> Question.objects.filter(id=1)
>>> Question.objects.filter(question_text__startswith='What')
>>> Question.objects.get(pub_date__year=current_year)
>>> q.choice_set.all()
```
#### By primary key
```
>>> Question.objects.get(pk=1) 
```
## Awesomness is we can use quesion object to create related choice record
```
 q = Question.objects.get(pk=1)
 ```
#### Display any choices from the related object set -- none so far.
```
>>> q.choice_set.all()
```
#### Create choices.
```
>>> q.choice_set.create(choice_text='Not much', votes=0)
```

### Accessing related objects
https://docs.djangoproject.com/en/2.2/ref/models/relations/

###  double underscores to perform field lookups via the API, 
https://docs.djangoproject.com/en/2.2/topics/db/queries/#field-lookups-intro

### Database API reference
https://docs.djangoproject.com/en/2.2/topics/db/queries/


## Creating an admin user
```
$ python manage.py createsuperuser

```

### URLS more -  URL patternfor example: /newsarchive/<year>/<month>/.
https://docs.djangoproject.com/en/2.2/topics/http/urls/

###  Some controlled coupling is introduced in the django.shortcuts module.
### ex: get_object_or_404()
https://docs.djangoproject.com/en/2.2/topics/http/shortcuts/#module-django.shortcuts


### templating 
https://docs.djangoproject.com/en/2.2/topics/templates/


### the 'name' value as called by the {% url %} template tag


```
path('<int:question_id>/', views.detail, name='detail')

<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```
as 'detail' function being called you can change the url from urls.py- no effect

### if multiple app are available such as polls app, following is the way it identify 
### which fucntion to use in app
APP NAME = polls       FUCNTION_NAME = details
```
<li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a></li>

```
### after adding above add following to mysite.app_name.urls.py
### ex: pollapp.polls.urs.py
```
app_name = 'polls'
```

### DONT FORGET {% csrf_token %} on forms 

### # Always return an HttpResponseRedirect after successfully dealing
### with POST data. This prevents data from being posted twice if a
### user hits the Back button.
 try:
 except:
 else:
    selected_choice.votes += 1
    selected_choice.save()  
    return HttpResponseRedirect(reverse('polls:results', args=(question.id,)))  
