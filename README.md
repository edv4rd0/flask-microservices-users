# flask-microservices-users

This is the results of me following the tutorial at: https://testdriven.io/

However, I had to make a change and remove a volume from the docker-compose users-service container as it overwrote the /usr/src/app directory that everything was copied into in the Dockerfile.

## Building and Running

To build and run: `docker-compose up -d --build`

Build: `docker-compose build`

Run: `docker-compose up -d`

### Create Database

First, create the databases and then seed with base user data.

```
docker-compose run users-service python manage.py recreate_db
docker-compose run users-service python manage.py seed_db
```

## Testing


To run all tests use the following command after running `docker-compose up -d`:

```
docker-compose run users-service python manage.py test
```


