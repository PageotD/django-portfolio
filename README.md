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
- [4. Add the Projects App](#4-add-the-projects-app)
    - [4.1 Create the `Projects` App](#41-create-the-projects-app)
    - [4.2 Define a Model](#42-define-a-model)
    - [4.3 Dive into Django Shell](#43-dive-into-django-shell)
    - [4.4 Create the View](#44-create-the-views)
    - [4.5 Craft the Templates](#45-craft-the-templates)
    - [4.6 Add the Routes](#46-add-the-routes)
    - [4.7 Leverage the Django Admin Site](#47-leverage-the-django-admin-site)
    - [4.8 Upload Images](#48-upload-images)

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

## 4. Add the Projects App

### 4.1 Create the `Projects` App

- to create the `Projects` app, run the following command:
    ```bash
    python manage.py startapp projects
    ```
- this will create a `projects` directory with several files (same as Pages)

- add the app in `personal_portfolio/settings.py`:
    ```python
    # ...

    INSTALLED_APPS = [
        "projects.apps.ProjectsConfig",
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

### 4.2 Define a Model

- to store data to display on a website, a database is required; Django has built-in support for databases with **object-relational mapping (ORM)**.
- an ORM is a program that allows to create classes that corespond to database tables; class attributes correspond to columns, and instances of the classes correspond to rows in the database.
- the classes that represent database tables are reffered to as **models**; in django they live in the models.py module of each Django app.

- the model to create is as follows:
    | Name | Description |
    | --- | --- |
    | title | A short string field to hold the name of the project |
    | description | A larger string field to hold a longer piece of text |
    | technology | A string field, but its contents will be limited to a select number of choices |

- to create the Project model, a new class is created in models.py:
    ```python
    from django.db import models

    class Project(models.Model):
        title = models.CharField(max_length=200)
        description = models.TextField()
        technology = models.CharField(max_length=20)
    ```

- CharField is a built-in model field type which works for short strings
- TextField is a built-in model field type which works for longer strings
- there is many other model field types: https://docs.djangoproject.com/en/5.1/ref/models/fields/#field-types

- by default, the Django ORM creates databases in SQLite, but other databases can be used as well, such as PostgreSQL or MySQL.

- to start the process of creating a database, a **migration** must be created
- a migration is a file containing a `Migration` class with rules that tell Django what changes need to be made to the database

- to create a migration, run the following command in django-portfolio/ :
    ```bash
    python manage.py makemigrations
    ```
    ```console
    Migrations for 'projects':
        projects/migrations/0001_initial.py
        - Create model Project
    ```
- a migrations/0001_initial.py file is created in the projects directory
- now that the migration is created, it can be applied to the database:
    ```bash
    python manage.py migrate projects
    ```
    ```console
    Operations to perform:
        Apply all migrations: projects
    Running migrations:
        Applying projects.0001_initial... OK
    ```

- when running both the makemigrations and migrate commands, projects is added to the commands; this tell django to only look at models and migrations in projects app.

- if running both the makemigrations and migrate commands without the projects flag, then all the migrations for all the default models in the Django app will be created and applied

- after the migrations a sqlite database is created in the personal_portfolio directory (db.sqlite3)

### 4.3 Dive Into the Django Shell

- to add new entries to Projects database table, an instance of the Project class must be created using the Django shell
- the Django shell is similar to the Python shell but allows to access the database and create entries:
    ```bash
    python manage.py shell
    ```
- then import the Project model
    ```python
    >>> from projects.models import Project
    ```

- then create an instance of the Project class
    ```python
    >>> first_project = Project(
    ...     title="My First Project",
    ...     description="A web development project.",
    ...     technology="Django",
    ... )
    >>> first_project.save()
    ```
- save() method creates a new entry in the project table and save it to the database
- create two additional projects in the database
    ```python
    >>> second_project = Project(
    ...     title="My Second Project",
    ...     description="Another web development project.",
    ...     technology="Flask",
    ... )
    >>> second_project.save()
    >>> third_project = Project(
    ...     title="My Third Project",
    ...     description="A final development project.",
    ...     technology="Django",
    ... )
    >>> third_project.save()
    >>> exit()
    ```

### 4.4 Create the Views

- create view functions to send the data from the database to the templates to display them on the portfolio site.
    - an index view that shows a snippett of information about each project
    - a detail view that shows more information on a particular topic

- both views will be added to the views.py file in the projects app directory
    ```python
    from django.shortcuts import render
    from projects.models import Project

    def project_index(request):
        projects = Project.objects.all()
        context = {
            "projects": projects,
        }
        return render(request, "projects/project_index.html", context)
    ```
- Project.objects.all() returns a list of all the Project instances in the database; it is a query to the database. The return object is a **QuerySet** object.
- the context dictionary has only one entry here, to which the QuerySet is assigned
- any entries in the context dictionary are available in the template, as long as the context is passed to render()

- create a project_detail view function in vews.py; this function will have primary key of the project that's being viewed as an additional argument:
    ```python
    def project_detail(request, pk):
        project = Project.objects.get(pk=pk)
        context = {
            "project": project,
        }
        return render(request, "projects/project_detail.html", context)
    ```
- this function performs a query to the database to get the project that has the primary key that is passed to the function as an argument

### 4.5 Craft the Templates

- create the templates in projects/templates/projects
    ```bash
    mkdir -p projects/templates/projects
    touch projects/templates/projects/project_index.html
    touch projects/templates/projects/project_detail.html
    ```

- for project_index.html Bootstrap cards will be created, with each card displaying details of the project.
    ```html
    {% extends "base.html" %}

    {% block page_content %}
    <h1>Projects</h1>
    <div class="row">
        {% for project in projects %}
        <div class="col-md-4">
            <div class="card mb-2">
                <div class="card-body">
                    <h5 class="card-title">{{ project.title }}</h5>
                    <p class="card-text">{{ project.description }}</p>
                    <a href="{% url 'project_detail' project.pk %}" class="btn btn-primary">
                        Read more
                    </a>
                </div>
            </div>
        </div>
        {% endfor %}
    </div>
    {% endblock page_content %}
    ```
- create project_detail.html
    ```html
    {% extends "base.html" %}

    {% block page_content %}
    <h1>{{ project.title }}</h1>
    <div class="row">
        <div class="col-md-4">
            <h5>About the project:</h5>
            <p>{{ project.description }}</p>
            <br>
            <h5>Technology used:</h5>
            <p>{{ project.technology }}</p>
        </div>
    </div>
    {% endblock page_content %}
    ```

### 4.6 Add the Routes

- create a projects/urls.py file in the projects app directory to hold the configuration
    ```python
    from django.urls import path
    from . import views

    urlpatterns = [
        path("", views.project_index, name="project_index"),
        path("<int:pk>/", views.project_detail, name="project_detail"),
    ]
    ```
- to connect to the detail view, url needs to include the primary key of the project as an argument
- the pk value in the URL is passed to the project_detail view function as an argument, so there is a need to dynamically generate theses URLs depending on which project to view using the <int:pk>/ pattern

- this notation tells Django that the value passed in the URL is an integer, and its variable name is pk.

- to hook theses URLs up to the main Django project's URL, in personnal_portfolio/urls.py add the following:
    ```python
    path("projects/", include("projects.urls")),
    ```
    ```python
    from django.contrib import admin
    from django.urls import path, include

    urlpatterns = [
        path("admin/", admin.site.urls),
        path("", include("personal_portfolio.urls")),
        path("projects/", include("projects.urls")),
    ]
    ```

### 4.7 Leverage the Django Admin Site

- Django reminds you that there were some unapplioed migratio ns when running the Django server
- apply the already existing migrations:
    ```bash
    $ python manage.py migrate

    Operations to perform:
        Apply all migrations: admin, auth, contenttypes, projects, sessions
    Running migrations:
        Applying contenttypes.0001_initial... OK
        Applying auth.0001_initial... OK
        Applying admin.0001_initial... OK
        Applying admin.0002_logentry_remove_auto_add... OK
        Applying admin.0003_logentry_add_action_flag_choices... OK
        Applying contenttypes.0002_remove_content_type_name... OK
        Applying auth.0002_alter_permission_name_max_length... OK
        Applying auth.0003_alter_user_email_max_length... OK
        Applying auth.0004_alter_user_username_opts... OK
        Applying auth.0005_alter_user_last_login_null... OK
        Applying auth.0006_require_contenttypes_0002... OK
        Applying auth.0007_alter_validators_add_error_messages... OK
        Applying auth.0008_alter_user_username_max_length... OK
        Applying auth.0009_alter_user_last_name_max_length... OK
        Applying auth.0010_alter_group_name_max_length... OK
        Applying auth.0011_update_proxy_permissions... OK
        Applying auth.0012_alter_user_first_name_max_length... OK
        Applying sessions.0001_initial... OK
    ```
- these are the features that Django already comes packed with. For example the authorization of users and the Django Admin Site.

- to access the Django Admin site, you must create an admin account for yourself first
    ```bash
    $ python manage.py createsuperuser
    Username (leave blank to use 'root'): admin
    Email address: admin@example.com
    Password: RealPyth0n
    Password (again): RealPyth0n
    Superuser created successfully.
    ```
- Start the Django development server again, visit http://localhost:8000/admin, and log in with your credentials

- This is your very own admin area! With just a few adjustments, it gives you the ability to manage your portfolio projects from the comfort of your browser window instead of the Django shell.

- To access your Project model in the Django admin site, you need to register the model first. Open the admin.py file of your projects app and add the code below:
    ```python
    from django.contrib import admin
    from projects.models import Project

    class ProjectAdmin(admin.ModelAdmin):
        pass

    admin.site.register(Project, ProjectAdmin)
    ```
- When you visit http://localhost:8000/admin again, you can spot that your projects are displayed on the admin site.

### 4.8 Upload Images

- It’s a good idea to promote your projects with images to grab the attention of your visitors. To do so, you’ll add a new field to the Project model and upload images in the Django admin site.

- Start by opening projects/models.py and adding an image field to it:
    ```python
    from django.db import models

    class Project(models.Model):
        title = models.CharField(max_length=100)
        description = models.TextField()
        technology = models.CharField(max_length=20)
        image = models.FileField(upload_to="project_images/", blank=True)
    ```

- FileField with a subfolder named project_images/. That’s where Django should store the images when you upload them.

- Django constructs the path to your upload folder using the MEDIA_ROOT setting and the upload_to value. To collect all the images in an uploads/ folder and serve them with a media/ URL, add these two lines to personal_portfolio/settings.py:
    ```python
    # ...

    MEDIA_URL = "/media/"
    MEDIA_ROOT = os.path.join(BASE_DIR, "media")
    ```

- The MEDIA_ROOT setting defines the folder that will store the media files in your file system. MEDIA_URL is the user-facing URL for the media folder that you present to visitors.

- To successfully serve media files, you also need to register the static routes to your media files in urls.py inside personal_portfolio/:
    ```python
    from django.contrib import admin
    from django.urls import path, include
    from django.conf import settings
    from django.conf.urls.static import static

    urlpatterns = [
        path("admin/", admin.site.urls),
        path("", include("pages.urls")),
        path("projects/", include("projects.urls")),
    ] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
    ```

- Since you made some changes to your Django model, you must create a new migration and then migrate the changes:
    ```bash
    (venv) $ python manage.py makemigrations projects
    Migrations for 'projects':
    projects/migrations/0002_project_image.py
        - Add field image to project

    (venv) $ python manage.py migrate projects
    Operations to perform:
    Apply all migrations: projects
    Running migrations:
    Applying projects.0002_project_image... OK
    ```
- Visit http://localhost:8000/admin and go to Projects. When you click on a project, you’ll see the new Image field and the option to choose a file

- Once all your projects contain images, you can go ahead and add them to your templates. Start with projects/templates/projects/project_index.html:
    ```html
    {% extends "base.html" %}

    {% block page_content %}
    <h1>Projects</h1>
    <div class="row">
    {% for project in projects %}
        <div class="col-md-4">
            <div class="card mb-2">
                {% if project.image %}
                    <img class="card-img-top" src="{{ project.image.url }}">
                {% endif %}
                <div class="card-body">
                    <h5 class="card-title">{{ project.title }}</h5>
                    <p class="card-text">{{ project.description }}</p>
                    <a href="{% url 'project_detail' project.pk %}"
                    class="btn btn-primary">
                        Read More
                    </a>
                </div>
            </div>
        </div>
        {% endfor %}
    </div>
    {% endblock %}
    ```

- Finally, you can add the image to the project_detail.html template. Just like before, you’ll wrap the image tag in an if condition:
    ```html
    {% extends "base.html" %}

    {% block page_content %}
    <h1>{{ project.title }}</h1>
    <div class="row">
        <div class="col-md-8">
            {% if project.image %}
                <img src="{{ project.image.url }}" width="100%">
            {% endif %}
        </div>
        <div class="col-md-4">
            <h5>About the project:</h5>
            <p>{{ project.description }}</p>
            <br>
            <h5>Technology used:</h5>
            <p>{{ project.technology }}</p>
        </div>
    </div>
    {% endblock page_content %}
    ```

## Conclusion
- Learned about the advantages of using Django
- Investigated the architecture of a Django site
- Set up a new Django project with multiple apps
- Built models and views
- Created and connected Django templates
- Uploaded images into your Django site