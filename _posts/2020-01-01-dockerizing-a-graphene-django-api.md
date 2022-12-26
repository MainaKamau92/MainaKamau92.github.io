---
layout: post
title: "Dockerizing a Graphene-Django API"
date: "2020-01-26"
tags: 
  - "django"
  - "django-graphene"
  - "docker"
  - "postgres"
---

In this walkthrough, we’ll be going through setting up a dockerized Graphql API made using the popular django-graphene library. 

What you require:

- Understanding of Django and Graphql concepts (we are not writing code perse)
- A computer
- Working Docker and docker-compose instances on your machine

With those requirements settled we can proceed.

The code I’ll be using can be located [here](https://github.com/MainaKamau92/conduit_django_graphene). The repository contains code of an API I made mocking the functionality of the online publishing platform [medium.com](http://www.medium.com). I prefer a real-world application to a todo app/api.  

### Dockerfile

On cloning the repository locally move into the root folder and create the `Dockerfile`, the file will hold a parent image of `python 3` and configurations to setup our conduit API.

According to docker [docs](https://docs.docker.com/compose/django/) official site, The Dockerfile defines an application’s image content via one or more build commands that configure that image. Once built, you can run the image in a container.

In the Dockerfile paste the following information in:

```
FROM python:3
ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1
RUN mkdir /conduit_api
WORKDIR /conduit_api
COPY requirements.txt /conduit_api/
RUN pip install -r requirements.txt
COPY . /conduit_api/
```

Our document starts with a `python 3` parent image. The parent image is modified by adding the new `conduit_api` directory. The parent image is further modified by installing the Python requirements defined in the `requirements.txt` file. `PYTHONDONTWRITEBYTECODE` prevents python from creating `pyc` files. We also create a new folder called `conduit_api` by running `mkdir` and making it our active folder by running `WORKDIR` on it. This is the folder that we will run our application from.

### Docker Compose file

The `docker-compose.yml` file describes the services that will make our API. It will look similar to this:

```
version: '3'

services:
  web:
    build: .
    command: python manage.py runserver 0.0.0.0:8080
    volumes:
    - .:/conduit_api
    ports:
    - "8080:8080"
    env_file:
      - .env
```

In the file, we define a web service labelled `web`, `web` is an arbitrary name and you can change it to any of your liking. It is important that the docker-compose version matches with the docker version, you can confirm that [here](https://docs.docker.com/compose/compose-file/).

We will proceed to hide our `SECRET_KEY` by removing it from the `settings.py` file and moving it into an environment variable that we will create in the root folder and label `.env`.

In your `settings.py` file make the following change:

```
SECRET_KEY = os.environ.get("SECRET_KEY")
```

Proceed to create a `.env` file in your root folder and add the value of your `SECRET_KEY`. In your `.env` add:

```
SECRET_KEY=y0urs3cretk3y
```

We can then proceed to build our image by running:

```
docker-compose build
```

**Note:** In the default case you might be required to run your docker-compose command as root, in such a case precede the above command with `sudo`.

## Postgres

Now we set up our Postgres DB to work with our existing implementation. We edit our `docker-compose` file and add the following changes

```
  db:
    image: postgres
    volumes:
      - postgres_data:/var/lib/postgresql/data/
    environment:
      - POSTGRES_USER=$USER
      - POSTGRES_PASSWORD=$USERSPASSWORD
      - POSTGRES_DB=$DB_NAME

volumes:
  postgres_data:
```

So what's happening? We define a Postgres image as well as a volume, the volume ensures the persistence of our data beyond the life of the container. We also include an `environment` segment that defines our Postgres DB credentials (ensure that you've already created a Postgres DB).

We can now proceed to build our image again and run our application.

```
docker-compose build 
```

On completion of the built, we should start up our container by running:

```
docker-compose up -d
```

The -d flag runs our container in the background.

You should be able to access your GraphQL API from the address:

```
http://0.0.0.0:8080/conduit/
```

## Conclusion

We have been able to successfully dockerize a Django graphene application and run it locally with Postgres DB persistence. I hope this has been informative, any questions? Write up a comment and I promise to reply ASAP.

## References

[Testdriven.io](https://testdriven.io/blog/dockerizing-django-with-postgres-gunicorn-and-nginx/)

[Docker Docs](https://docs.docker.com/compose/django/)
