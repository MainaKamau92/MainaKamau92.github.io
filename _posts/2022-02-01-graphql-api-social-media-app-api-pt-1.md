---
title: "GraphQL API (Social Media App API) pt 1"
date: "2022-02-03"
---

Over the course of the coming weeks, I'll be attempting to build a social site backend API using GraphQL. I'll be covering some key concepts in GraphQL such as queries and mutations.

## Prerequisites

I'll strive to explain most if not all steps along the way, however, a basic understanding of the python programming language will go a long way in following along.

## What is GraphQL?

According to the official website,

> GraphQL is a query language for APIs and a runtime for fulfilling those queries with your existing data.

## Some of the tenets that make up GraphQL include:

### Data fetching:

Unlike with a REST-based API, with GraphQL you are able to fetch a comprehensive data collection without having to query multiple endpoints. You need only send a single query with the data requirements and get back just what you requested.

### What you ask is what you get:

In GraphQL you get exactly what you query for. Each query responds with data and only the data requested, no more no less.

You can find a more comprehensive GraphQL vs REST explanation [here](https://www.howtographql.com/basics/1-graphql-is-the-better-rest/).

## Parts of the API

I'll make it a point to group related functionality into apps. This will greatly help in structuring the API and achieve some degree of loose coupling, with unrelated aspects of the API interacting only when necessary. I might need to add more, but the beginner candidates include:

- Authentication App
- Friends App
- Profile App

## Project Set-Up

You can start off by creating a project folder by running the command `mkdir GraphQLSocial` in the terminal from your home folder preferably,

> I'll be using a unix environment, this means for users of the windows OS you might need to check the windows shell equivalent of the commands i'll be running. Alternatively you might consider developing on the Windows Subsystem for Linux or a unix based terminal.

The command creates a folder where all your code for the project will live. You'll be using Django to develop the API, it'll help offload some effort, by for example providing an inbuilt ORM for you to interact with the DB more easily.

Some other packages that'll be essential in development include [psycopg2](https://www.psycopg.org/), a python wrapper for Postgres as well as [django-graphene](https://docs.graphene-python.org/projects/django/en/latest/) a GraphQL wrapper made specifically for Django it is based on the python graphene [package](https://graphene-python.org/).

Let's begin by installing the requirements. From the root directory (created earlier) run the below commands in sequence.
```
touch .python-version  &&  echo "graphqlsocialenv" >> .python-version
pyenv virtualenv 3.8.6 graphqlsocialenv
pip install django psycopg2 graphene-django
```
Firstly, you create a `.python-version` file that'll be referenced every time you `cd` into the folder and will automatically activate the python environment. The second command creates and automatically activates the python environment, the final line installs all our dependencies.

> As a side note if not already you should install a PostgreSQL instance on your machine from [here](https://www.postgresql.org/download/).I'll be using it as the Database of choice. You can also get this [extension](https://marketplace.visualstudio.com/items?itemName=mainakamau.graphenesnippets) if you'll be using VS Code as the editor of choice.

Once you have all the dependencies installed you can proceed to create the Django project. You'll do so by running the command
```
django-admin startproject social . 
```

The command creates a project that comes with a bunch of files that'll be useful as we proceed (note the period after social) your folder structure should resemble:
```
.
├── manage.py
└── social
    ├── __init__.py
    ├── asgi.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```
Next, you'll create the first app by running from the root folder the command:
```
django-admin startapp authentication
```
The command will create an app folder named authentication with a migrations folder that'll keep track of your DB fields changes. You'll need to add the app to the `INSTALLED_APPS` list in your `settings.py` file. You'll also need to add `graphene_django` app in the `INSTALLED_APPS` list as well. The `INSTALLED_APPS` list should resemble:

```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'graphene_django',
    'authentication'
]
```

Your folder structure should now resemble:

```
.
├── authentication
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   └── views.py
├── manage.py
└── social
    ├── __init__.py
    ├── asgi.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```

Moving forward, that's the pattern you'll be following when creating a new app.

Having completed the initial setup,you should proceed to run the server and see what you have so far, below is the command
```
python manage.py runserver
```
You should receive a warning that you have unapplied migrations, let's clear out that error by applying the migrations. Run:
```
python manage.py migrate
```
Running the server again the error disappears. Navigating to the address `http://127.0.0.1:8000/` you should be met with the Django default page. This is an indication that the app is set up correctly.

Next, you'll create a model for the User, instead of creating everything from scratch you'll be borrowing a few 'parts' from the default Django auth model. You'll do this by creating your own model and inheriting from the Django `BaseUserManager`. Add the below code to your `authentication/models.py` file.
```
from django.contrib.auth.base_user import AbstractBaseUser
from django.core.exceptions import ValidationError
from django.db import models
from django.contrib.auth.models import BaseUserManager, PermissionsMixin
```
# Create your models here.
```
class UserManager(BaseUserManager):
    """
    Django requires that custom users define their own Manager class. By
    inheriting from `BaseUserManager`, we get a lot of the same code used by
    Django to create a `User` for free.
    All we have to do is override the `create_user` function which we will use
    to create `User` objects.
    """

    def create_user(self, username, email, password=None):
        if email is None:
            raise ValidationError("Users must have and email.")

        user = self.model(username=username, email=self.normalize_email(email))
        user.set_passsword(password)
        user.save()
        return user

class User(AbstractBaseUser, PermissionsMixin):
    username = models.CharField(max_length=255, null=True)

    # We want to index this column in the database to improve lookup performance.
    email = models.EmailField(db_index=True, unique=True, null=False)
    is_active = models.BooleanField(default=True)
    is_staff = models.BooleanField(default=False)

    USERNAME_FIELD = 'email' # indicates to django to use this as the login field instead of the username

    # Tells Django that the UserManager class defined above should manage
    # objects of this type.
    objects = UserManager()

    def __str__(self):
        return self.email
    
```
The comments in the code should explain much of what is going on, you are using the already existing `auth model/structure` and overriding aspects you prefer customized to your own needs. Note, you'll be using the email and not the username for authentication purposes.

You'll need to inform Django of the new custom auth model, by adding this line to the `settings.py` file.
```
AUTH_USER_MODEL = 'authentication.User'
```
You can run the migrations again to update the DB. Speaking of which we forgot to plugin the Postgres information, let's do that now. In your settings file replace the DATABASES field with this.
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': os.getenv('DB_NAME', 'postgres'),
        'USER': os.getenv('DB_USER', 'postgres'),
        'PASSWORD': os.getenv('DB_PASSWORD', 'postgres'),
        'HOST': os.getenv('HOST', 'localhost'),
        'PORT': os.getenv('PORT', 5432),
    }
}
```
The above snippet makes Postgres our default database, you'll notice I've used environment variables for the sensitive parts of the DB information, this comes highly recommended as a security precaution. Remember to import the os module.

Create a `.env` file at the root of your project and add your env variables there. Mine looks like this:
```
export DB_NAME='your_db_name'
export DB_USER='your_db_user'
export DB_PASSWORD='your_db_password'
```
Add the variables by running:
```
source .env
```
Now you may proceed to run the migrations, by executing these commands sequentially:
```
python manage.py makemigrations
python manage.py migrate
```
The commands will create a new file in `authentication/migrations` folder, tracking the new changes made. Now that you have the model part finalized you'll proceed to work on the schemas and mutations.

We'll be using JsonWebTokens as our authorization mechanism, in that regard we'll need a package that'll make our life easier, install the package by running:
```
pip install django-graphql-jwt
```
You'll also need to add a few more configurations in the settings file:
```
GRAPHENE = {
    'SCHEMA': 'social.schema.schema',
    'MIDDLEWARE': [
        'graphql_jwt.middleware.JSONWebTokenMiddleware',
    ],
}
AUTHENTICATION_BACKENDS = [
    'graphql_jwt.backends.JSONWebTokenBackend',
    'django.contrib.auth.backends.ModelBackend',
]
```
## Schemas, Queries and Mutations

In your `social` folder you'll add a `schema.py` file and in it add below code:
```
import graphene
from authentication.schemas import AuthMutation, AuthQuery

class Query(AuthQuery, graphene.ObjectType):
    pass

class Mutation(AuthMutation, graphene.ObjectType):
    pass

schema = graphene.Schema(query=Query, mutation=Mutation)
```
Don't worry about the errors as a result of the inexistent files, you'll create them next. In your authentication app folder create a new python package and label it `schemas`, it'll hold our mutation and query files. Inside the package create two new files, `auth\_queries.py` and `auth\_mutations.py`. Your folder structure so far should resemble:
```
.
├── authentication
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── migrations
│   │   ├── 0001_initial.py
│   │   ├── __init__.py
│   │   └── __pycache__
│   ├── models.py
│   ├── schemas
│   │   ├── __init__.py
│   │   ├── auth_mutations.py
│   │   └── auth_queries.py
│   └── views.py
├── manage.py
└── social
    ├── __init__.py
    ├── asgi.py
    ├── schema.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```
## User Registration Mutation

You'll begin with the `auth_mutations` file, mutations in GraphQL alter the data, they handle the CUD part of the CRUD abbreviation, the Create, Update and Delete capabilities of the API. The `auth_mutations` file should have the following code:
```
import graphene
from django.db import transaction
from graphene_django import DjangoObjectType

from authentication.models import User

class UserType(DjangoObjectType):
    class Meta:
        model = User

class RegisterUser(graphene.Mutation):
    user = graphene.Field(UserType)

    class Arguments:
        username = graphene.String()
        email = graphene.String()
        password = graphene.String()

    errors = graphene.String()
    success_message = graphene.String()
    token = graphene.String()

    
    def mutate(self, info, **kwargs):
        username = kwargs.get('username')
        email = kwargs.get('email')
        password = kwargs.get('password')

        user_data = dict(username=username, email=email, password=password)
        message = "User created {} successfully.".format(email)

        with transaction.atomic():
            user = User(**user_data)
            user.set_password(kwargs.get('password'))
            user.save()
            return RegisterUser(user=user, success_message=message)

class Mutation(graphene.ObjectType):
    register_user = RegisterUser.Field()
```
You'll start off with the **User** `registration/signup mutation`. The UserType class is a `django-graphene` helper that transforms our User model into an object type. You'll then proceed to write a RegisterUser class which will inherit from `graphene.Mutation` class, you'll override the mutate class and this is where you'll add your custom code to add a new user into the DB.

You'll fetch all the fields supplied by the user which will be passed as keyword arguments in the mutate class. The class Arguments defines the expected data types from our user, in our case all are Strings. We wrap the save sequence in an atomic block to mitigate against data corruption (we roll back all changes in case something goes wrong).

You'll proceed to add the below code to the `__init__.py` file in the schemas folder.
```
from .auth_mutations import Mutation as AuthMutation
```
In the `schema.py` file remove any mention of Query and remain only with the Mutation. That should remove the errors that were there earlier.

Finally, you'll need to edit the `urls.py` file, with a single endpoint for your GraphQL API, that's right a single endpoint to rule them all. Add the code below to your urls.py:
```
.....
#intial code remains
from django.views.decorators.csrf import csrf_exempt
from graphene_django.views import GraphQLView

urlpatterns = [
    ...
    path('social/', csrf_exempt(GraphQLView.as_view(graphiql=True))),
]
```
## The First User

Visiting `http://127.0.0.1:8000/social/` should present you with a GraphQL page from where you can run all manner of commands, it should resemble:

![](https://res.cloudinary.com/dae3oj71g/image/upload/v1672044248/GithubPages/graphql-api-pt-1/Screenshot-2022-01-29-at-12.59.20-1024x375.png)

We can proceed to register a new user by typing the below command on the left top side of the screen, feel free to delete the comments.
```
mutation RegisterUser($email: String!, $username: String!, $password: String) {
  registerUser(email: $email, username: $username, password: $password){
    user {
      email
    }
    successMessage
    errors
  }
}
```
The statements preceded by `$` are variables and you'll need to provide the values, you should see a section on the bottom left labelled `QUERY VARIABLES` click on it and add below section:
```
{
  "email": "email@email.com",
  "username": "username",
  "password": "pass1234"
}
```
The above should be indicative of credentials a user would plug-in during normal registration. Finally, click on the play button above and you should see a success/failure message on the right side of the screen. That should do it for the registration mutation.

## Fetching Users

Let's tackle the Query, in the `auth_queries.py` file add:
```
import graphene
from graphene_django import DjangoObjectType
from graphql import GraphQLError
from ..models import User

class QueryUserType(DjangoObjectType):
    class Meta:
        model = User
        fields = "__all__"

class Query(graphene.ObjectType):
    user = graphene.Field(QueryUserType, id=graphene.Int(required=True))
    users = graphene.List(QueryUserType)

    def resolve_users(self, info):
        return User.objects.all()

    def resolve_user(self, info, **kwargs):
        id = kwargs.get('id')
        try:
            return User.objects.get(pk=id)
        except:
            raise GraphQLError("User does not exist")
```
In the `schemas/__init__.py` file add;
```
from .auth_queries import Query as AuthQuery
```
Finally, your `schema.py` file should look like this:
```
import graphene
from authentication.schemas import AuthMutation, AuthQuery

class Query(AuthQuery, graphene.ObjectType):
    pass

class Mutation(AuthMutation, graphene.ObjectType):
    pass

schema = graphene.Schema(query=AuthQuery, mutation=Mutation)
```
You can test out the Query by running the Query below from the someplace you did the mutation:
```
{
  users {
    username
    email
  }
}
```
You can add more fields in the users' brackets to get a larger return object e.g. id. To fetch a single user we alter the query to resemble:
```
{
  user(id: 1) {
    username
    email
  }
}
```
## The Login Flow

Now that you have the registration working we can start working on the login process, which will be our last part in the authentication flow. Let's head back to the `auth_miutations` file and make a couple of edits to the file.

You'll start by adding a new `LoginMutation` class that will hold your login logic. Add the below code into the file:
```
# registration code remains
class LoginMutation(graphene.Mutation):
    user = graphene.Field(UserType)
    errors = graphene.String()
    token = graphene.String()
    success = graphene.String()

    class Arguments:
        email = graphene.String()
        password = graphene.String()

    def mutate(self, info, **kwargs):
        email = kwargs.get("email")
        password = kwargs.get("password")
        user = authenticate(username=email, password=password)

        error_message = "Invalid login credentials"
        success_message = "Logged in!"

        if user:
            token = jwt_encode(jwt_payload(user))
            return LoginMutation(user=user, token=token, success=success_message)
        else:
            return LoginMutation(errors=error_message)

class Mutation(graphene.ObjectType):
    register_user = RegisterUser.Field()
    login_user = LoginMutation.Field()
```
Most of the code in this mutation mirrors the one in the RegisterUser mutation and we'll not go through it again. Noteworthy is the `authenticate` function supplied to us by Django, it verifies that the user attempting to login actually exists in the system and the credentials are valid.

We check for a user or None return type, if the former we use the returned user object to generate a JSON web token for authorization purposes moving forward. If the latter we return an error message communicating invalid credentials. We can test this out.

Head to the GraphQL UI and just as we did the registration process add the required variables below while adding the mutation on the left side, my mutation resembles:
```
mutation loginUser($email: String!, $password: String!) {
  loginUser(email: $email, password: $password) {
    user {
      email
      username
    }
    token
    success
    errors
  }
}
```
And the variables:
```
{
"email": "email@email.com",
"password": "pass1234"
}
```
If the credentials checkout, you should receive a response with a login successful message on top of the JsonWebToken for authorization.

This concludes the first part of this series and we'll start on the Profile App the next time.

The code so far can be found [here](https://github.com/MainaKamau92/graphgqlsocial).

Check out my other works [here](https://mainakamau92.github.io/)
