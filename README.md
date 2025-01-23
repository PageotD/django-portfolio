# django-portfolio

Based on the [Real Python tutorial](https://realpython.com/get-started-with-django-1/).

**TOC**
- [1. Understand the structure of a Django Website](#1-understand-the-structure-of-a-django-website)
- [2. Create the Example Portfolio Project With Django](#2-create-the-example-portfolio-project-with-django)
    - [2.1 Setup the Development Environment](#21-setup-the-development-environment)
    - [2.2 Start the Django Project](#22-start-the-django-project)
- [3. Add the Pages App](#3-add-the-pages-app)

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