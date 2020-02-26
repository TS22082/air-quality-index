# check versions!

python --version
pip --version

make sure everything is set to python 3 and pip 19+

maybe you downloaded some stuff. Lets check

```
pip freeze
```

If you have django thats cool but having virtual envirements to hold your bigger fluntuating dependiencies is better.

```
pip install virtualenv
```

run pip freeze again
ls

- you should have virtualenv 20.+

# create a virtual envirement

you have to run that activate script

To activate:

Mac:

```
source/bin/activate
```

Windows:

```
source venv/Scripts/activate
```

To deactivate:

```
deactivate
```

Now run pip freeze when your venv is activated

# Install Django in the virtual envirement

```
pip install Django
```

## Start a Project

```
django-admin startproject weather
```

Navigate into the folder that was just created
ls to make sure you see a mange.py

run:

```
python manage.py runserver
```

open http:localhost:8000

# Create an app in the project

/weather

```
python manage.py startapp lookup
```

first thing to do is to navigate to weather/settings.py and add 'lookup' to INSTALLED_APPS

# Setting a client route

- create a urls.py file in lookup/
- copy contents from weather/urls.py to lookup/urls.py

change weather/urls.py to :

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('lookup.urls'))
]
```

In lookup we need somethig to see

- Create a "templates" folder in /lookup

- in lookup/views.py

```python
from django.shortcuts import render

def home(request):
    return render(request, 'home.html', {})
```

navigate to /lookup/urls.py

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.home, name="home"),
]
```

in templates create a base.html file

/lookup/base.html

```
my header

{% block content %}

{% endblock %}


my footer
```

then modify your home page

```
{% extends 'base.html' %}

{% block content %}

    <h1>Hello World</h1>

{% endblock %}

```

Add another template 'about'

lookup/templates/about.html

```
{% extends 'base.html' %}

{% block content %}

    <h1>About me</h1>

{% endblock %}

```

lookup/urls.py

```python

from django.urls import path
from . import views

urlpatterns = [
    path('', views.home, name="home"),
    path('about', views.about, name="about"),
]

```

lookup/views.py

```python
def about(request):
    return render(request, 'about.html', {})

```

# Implementing bootstrap

head to bootstrap getting started page and copy the template over

modify the links to navigate to home or about

/templates/base.html
```
<a class="navbar-brand" href="/">Weather</a>
<a class="navbar-brand" href="/">About</a>
```

full:

```
<!doctype html>
<html lang="en">
  <head>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">

    <title>Hello, world!</title>
  </head>
  <body>

    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
        <a class="navbar-brand" href="/">Weather</a>
        <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
          <span class="navbar-toggler-icon"></span>
        </button>

        <div class="collapse navbar-collapse" id="navbarSupportedContent">
          <ul class="navbar-nav mr-auto">
            <li class="nav-item">
              <a class="nav-link" href="/about">About</a>
            </li>
          </ul>
          <form class="form-inline my-2 my-lg-0">
            <input class="form-control mr-sm-2" type="search" placeholder="Search" aria-label="Search">
            <button class="btn btn-outline-primary my-2 my-sm-0" type="submit">Search</button>
          </form>
        </div>
      </nav>


    <div class="container">
        <br>
        {% block content %}

        {% endblock %}
    </div>



    <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
    <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>
  </body>
</html>

```

these are hard coded links, django has a better way of doing this
django links:

```
<a class="nav-link" href="{% url 'home' %}">Home</a>
<a class="nav-link" href="{% url 'about' %}">About</a>
```

now that you have set up the navbar we need to show content on the home page.

/lookup/views

from django.shortcuts import render


# Create your views here.

```python

def home(request):
    import json
    import requests

    api_request = requests.get("http://www.airnowapi.org/aq/forecast/zipCode/?format=application/json&zipCode=94610&date=2020-02-25&distance=5&API_KEY=39FDC89B-4B3C-4FAE-8E9B-9EEC1DC0086E")

    try:
        api = json.loads(api_request.content)
    except Exception as e:
        api = "Error..."

    return render(request, 'home.html', {'api' : api })

def about(request):
    return render(request, 'about.html', {})

```

We need to format this data data and render it to the screen

lookup/templates/home.html

```
{% extends 'base.html' %}

{% block content %}

    <h1>Hello World</h1>
    <br/>
    {{ api.0 }}
    <br/>
    <br/>
    Air quality index: {{ api.0.AQI }}
    <br/>
    Reporting area: {{ api.0.ReportingArea }}
    <br/>
    Description: {{ api.0.Category.Name }}
{% endblock %}

```

Still not pretty but you get the point

One thing we should do is do some error handling:

lookup/templates/home.html
```
{% extends 'base.html' %}

{% block content %}

    <h1>Hello World</h1>

    {% if api %}
        {% if api == "Error..." %}
            There was an error please try again
        {% else %}
            <br/>
            {{ api.0 }}
            <br/>
            <br/>
            Air quality index: {{ api.0.AQI }}
            <br/>
            Reporting area: {{ api.0.ReportingArea }}
            <br/>
            Description: {{ api.0.Category.Name }}
        {% endif %}
    {% endif %}
{% endblock %}

```

Now we style with bootstrap


```
{% extends 'base.html' %}

{% block content %}

    {% if api %}
        {% if api == "Error..." %}
            There was an error please try again
        {% else %}
            <br/>
            <div class="jumbotron">
                <h1 class="display-4">{{ api.0.Category.Name }}</h1>
                <p class="lead">{{ api.0.ReportingArea}}s air quality index is currently {{ api.0.AQI }}</p>
                <hr class="my-4">
                <p>Air quality index: {{ api.0.AQI }}</p>

                {% if api.0.Category.Name == "Good" %}
                    <p>AQI: Good (0 - 50) Air quality is considered satisfactory, and air pollution poses little or no risk.</p>

                    {% elif  api.0.Category.Name == "Moderate" %}
                        <p>AQI: Good (0 - 50) Air quality is considered satisfactory, and air pollution poses little or no risk.</p>

                    {% elif  api.0.Category.Name == "Unhealthy for Sensitive Groups" %}
                        <p>AQI: Moderate (51 - 100) Air quality is acceptable; however, for some pollutants there may be a moderate health concern for a very small number of people who are unusually sensitive to air pollution.</p>

                    {% elif  api.0.Category.Name == "Unhealthy" %}
                        <p>AQI: Unhealthy for Sensitive Groups (101 - 150) Although general public is not likely to be affected at this AQI range, people with lung disease, older adults and children are at a greater risk from exposure to ozone, whereas persons with heart and lung disease, older adults and children are at greater risk from the presence of particles in the air.</p>

                    {% elif  api.0.Category.Name == "Very Unhealthy" %}
                        <p>AQI: Very Unhealthy (201 - 300) Health alert: everyone may experience more serious health effects.</p>

                    {% elif  api.0.Category.Name == "Hazardous" %}
                        <p>AQI: Hazardous (301 - 500) Health warnings of emergency conditions. The entire population is more likely to be affected.</p>

                {% endif %}

            </div>
        {% endif %}
    {% endif %}
{% endblock %}

```

we want to add styles based on the air quality:




lookup/templates/base.html

```
{% extends 'base.html' %}

{% block content %}

    {% if api %}
        {% if api == "Error..." %}
            There was an error please try again
        {% else %}
            <br/>
            <div class="jumbotron {{ category_color }}">
                <h1 class="display-4">{{ api.0.Category.Name }}</h1>
                <p class="lead">{{ api.0.ReportingArea}}s air quality index is currently {{ api.0.AQI }}</p>
                <hr class="my-4">
                <p>Air quality index: {{ api.0.AQI }}</p>

                {% if api.0.Category.Name == "Good" %}
                    <p>AQI: Good (0 - 50) Air quality is considered satisfactory, and air pollution poses little or no risk.</p>

                    {% elif  api.0.Category.Name == "Moderate" %}
                        <p>AQI: Good (0 - 50) Air quality is considered satisfactory, and air pollution poses little or no risk.</p>

                    {% elif  api.0.Category.Name == "Unhealthy for Sensitive Groups" %}
                        <p>AQI: Moderate (51 - 100) Air quality is acceptable; however, for some pollutants there may be a moderate health concern for a very small number of people who are unusually sensitive to air pollution.</p>

                    {% elif  api.0.Category.Name == "Unhealthy" %}
                        <p>AQI: Unhealthy for Sensitive Groups (101 - 150) Although general public is not likely to be affected at this AQI range, people with lung disease, older adults and children are at a greater risk from exposure to ozone, whereas persons with heart and lung disease, older adults and children are at greater risk from the presence of particles in the air.</p>

                    {% elif  api.0.Category.Name == "Very Unhealthy" %}
                        <p>AQI: Very Unhealthy (201 - 300) Health alert: everyone may experience more serious health effects.</p>

                    {% elif  api.0.Category.Name == "Hazardous" %}
                        <p>AQI: Hazardous (301 - 500) Health warnings of emergency conditions. The entire population is more likely to be affected.</p>

                {% endif %}

            </div>
        {% endif %}
    {% endif %}
{% endblock %}

```

Our home.html has gotten way to cluttered and we need sould let the view handle alot of this work

