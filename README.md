# django-portfolio

Based on the [Real Python tutorial](https://realpython.com/get-started-with-django-1/).

**TOC**
- [1. Understand the structure of a Django Website](#1-understand-the-structure-of-a-django-website)
- [2. Create the Example Portfolio Project With Django](#2-create-the-example-portfolio-project-with-django)
    - [2.1 Setup the Development Environment](#21-setup-the-development-environment)
    - [2.2 Start the Django Project](#22-start-the-django-project)
- [3. Add the Pages App](#3-add-the-pages-app)
    - [3.1 Create the `Pages` App](#31-create-the-pages-app)
    - [3.2 Create a View](#32-create-a-view)
    - [3.3 Add a Route](#33-add-a-route)

## 1. Understand the structure of a Django Website

- single **project** project (a web application) splits into separate **apps**.
- each **app** handles a self-contained **task** like:
    - **user management**: logging in and out
    - **image feed**: uploading, editing, and displaying images
    - **private messaging**: sending messages between users and providing notifications

- some configurations apply to all apps (project as a whole) such as project setting, URLs, shared templates, and static files.

- each app can have its own database and its own functions to control how it display data to the user in HTML templates.

- support the **Model-View-Template (MVT)** design pattern based on the **Model-View-Controller (MVC)** pattern:
    - **Model** defines the data structure.
    - **View** displays some or all of the data to the user with HTML and CSS.
    - **Controller** handles how the database and the view interact.

    > **Note:** The view and template in Django’s MVT pattern make up the view in the MVC pattern of other web frameworks.

## 2. Create the Example Portfolio Project With Django

- Django project will have the following apps:
    - **Pages**: This Django app will give a first impression on how to display content to the website's visitors.. It is a great starting point that can be enhanced later to meet other needs.
    - **Projects**: This app will showcase projects. A gallery style page with clickable links to projects.

- Django has many features included so there is no need to consider external python packages. However, here the Bootstrap framework is included and will be used for styling.

### 2.1 Setup the Development Environment

- create and activate a virtual environment
    ```bash
    python3 -m venv .env-portfolio
    source .env-portfolio/bin/activate
    ```

- install Django
    ```bash
    pip install django
    ```

### 2.2 Start the Django Project

- Make sure to be in the root of the repository and run the following command to create the project
    ```bash
    django-admin startproject personal_portfolio .
    ```

- `startproject` command will create a new Django project named `personal_portfolio` in the current directory. The directory structure will look like this:
    ```
    .
    ├── manage.py
    ├── personal_portfolio
    │   ├── __init__.py
    │   ├── asgi.py
    │   ├── settings.py
    │   ├── urls.py
    │   └── wsgi.py
    └── .env-portfolio
    ```

- start the django **development server** and check that the setup was successful:
    ```bash
    python manage.py runserver  # starts the development server at http://127.0.0.1:8000
    ```

## 3. Add the Pages App

### 3.1 Create the `Pages` App

- to create the `Pages` app, run the following command:
    ```bash
    python manage.py startapp pages
    ```

- this will create a `pages` directory with several files:
    - `__init__.py` tells python to treat the directory as a Python packages
    - `admin.py` contains settings for the django admin pages
    - `apps.py` contains settings for the `pages` app
    - `models.py` contains a series of classes that Django's ORM converts to database tables
    - `tests.py` contains test classes
    - `views.py` contains functions and classes that handle what data is displayed in the HTML Templates

- to install the app in the project, in `django-portfolio/personal_portfolio/settings.py`, add `pages.apps.PagesConfig`:
    ```python
    # ...

    INSTALLED_APPS = [
        "pages.apps.PagesConfig",
        "django.contrib.admin",
        "django.contrib.auth",
        "django.contrib.contenttypes",
        "django.contrib.sessions",
        "django.contrib.messages",
        "django.contrib.staticfiles",
    ]

    # ...
    ```

### 3.2 Create a View

- a view in Django is a collection of functions or classes inside the `views.py` file in an app's directory.
- each function or class handles the logic that the app processes each time the user visits a different URL.

- add the following code to `view.py`in the `pages` directory:
    ```python
    from django.shortcuts import render

    # Create your views here.
    def home(request):
        return render(request, "pages/home.html", {})
    ```
- the render function looks for HTML templates inside a directory called `templates`.

- since templates of different apps can have the same names, it s a good practice to add a subdirectory with the app's name inside the `templates` directory.

- create a templates directory and a pages/ subdirectory in the pages/ app directory
    ```bash
    mkdir -p templates/pages
    ```

- create a home.html file in the templates/pages/ subdirectory:
    ```html
    <h1>Hello, World</h1>
    ```

### 3.3 Add a Route

- `personal_portfolio` contains a file names `urls.py` in which a url configuration (route) can be added
    ```python
    from django.contrib import admin
    from django.urls import path, include

    urlpatterns = [
        path("admin/", admin.site.urls),
        path("", include("pages.urls")),
    ]
    ```

- now the `pages.url` is needed, create the pages/urls.py file:
    ```python
    from django.urls import path

    from . import views

    urlpatterns = [
        path("", views.home, name="home"),
    ]
    ```
- this file contains a list of URL patterns that correspond to the various view functions (only home for now)

- run the dev server again and visit the homepage at http://127.0.0.1:8000
    ```bash
    python manage.py runserver  # starts the development server at http://127.0.0.1:8000
    ```

### 3.4 Add Bootstrap to the App

- add an external CSS framework: Bootstrap
- first create a base template in a `templates` folder in django-portfolio
    ```bash
    mkdir templates
    touch templates/base.html
    ```
- for templates that the whole project shares, it is a good idea to create a templates/ directory in the root directory

- inside base.html:
    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="utf-8">
        <title>{% block title %}My Personal Portfolio{% endblock title %}</title>
        <link
            href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.1/dist/css/bootstrap.min.css"
            rel="stylesheet"
        >
    </head>
    <body class="container">
    {% block page_content %}{% endblock page_content %}
    </body>
    </html>
    ```
- the prevous code is a skeleton of a valid HTML document, all future templates created will extend this base template and inherit the bootstrap styling on every page without having to import the styles again.

- with `{% block %}` **template tags** it is possible to define content blocks that can be used or override in **child templates** that extend the parent template. home.html can be modified:
    ```html
    {% extends "base.html" %}
    {% block page_content %}
    <h1>Hello, World</h1>
    {% endblock page_content %}
    ```

- note that the page_content blovk is overrided but not the title block.

- add the base.html by updating the TEMPLATES list in personal_portfolio/settings.py (add BASE_DIR / "templates/" to the list):
    ```python
    # ...

    TEMPLATES = [
        {
            "BACKEND": "django.template.backends.django.DjangoTemplates",
            "DIRS": [
                BASE_DIR / "templates/",
            ],
            "APP_DIRS": True,
            "OPTIONS": {
                "context_processors": [
                    "django.template.context_processors.debug",
                    "django.template.context_processors.request",
                    "django.contrib.auth.context_processors.auth",
                    "django.contrib.messages.context_processors.messages",
                ]
            },
        }
    ]

    # ...
    ```