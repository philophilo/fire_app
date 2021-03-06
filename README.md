[![CircleCI](https://circleci.com/gh/philophilo/fire_app.svg?style=shield)](https://app.circleci.com/pipelines/github/philophilo/fire_app?filter=all)

# Introduction

The application is written in Python and Django as the framework. It provides an api that captures expenses, income and provides statistics for both. The application can be run locally for development and deployments to production are handled through Circleci. The application runs in an EKS cluster for public access. See [infrastructure repo](https://github.com/philophilo/fire_k8s).

<img width="1658" alt="Screenshot 2022-01-29 at 02 25 07" src="https://user-images.githubusercontent.com/12629658/151635049-732255ef-f2be-432a-b4a8-f38eb31aa654.png">

### Requirements
```
- GNU Make
- Docker
- Docker-compose
- SMTP Server (Gmail recommended)
```
[How to setup Gmail SMTP server](https://kinsta.com/blog/gmail-smtp-server/)

### Setup
Clone the repository and create `.env` file in the docker directory and add the following creadentials

```
git clone https://github.com/philophilo/fire_app.git
```
Required credentials

```
DATABASE_USER=
DATABASE_PASSWORD=
DATABASE_NAME=
DATABASE_HOST=
DATABASE_PORT=5432
SECRET_KEY=
EMAIL_HOST='smtp.gmail.com'
EMAIL_PORT=587
EMAIL_HOST_USER=
EMAIL_HOST_PASSWORD=
```
The application uses Postgres as the database. This is also setup through Docker. The variables in `docker/.env` will be picked up by docker-compose on setup.

### Running the application

When the credentials have been setup, run `make run-app`. The application will be running on `localhost:8000`

### The necessary Make commands

`make network` Creates the docker-compose network

`make build` Builds the application's image through docker-compose

`make up` Creates the containers with `docker-compose up`

`make run-app` Starts the application through `start_app.sh` script which runs gunicorn

`make stop` Stops the application but the database continues running

`make pip-install` Installs python packages inside the container 

`make pip-reinstall` Reinstalls python packages in `requirements.txt`

`make down` Stops all containers and removes them

`make migrate` Creates Django migrations

`make migrations` Applies Django migrations

`make django-app` Creates a Django application

`make collectstatic` Collects static files from all Django applications

`make django-shell` Opens a Django shell with IPython

`make exp-shell` Opens the docker container shell.

`make tests` Runs Django tests

`make build-ci` Builds the Docker image used to run tests in Circleci

`make push-ci` Pushes the Circleci to Docker hub

### Continuous Integration (CI)
The application uses CircleCi for tests and deployments. Other branches can run tests but the deployment is reserved as master branch. The deployment step [triggers](https://github.com/philophilo/fire_app/blob/master/.circleci/config.yml#L47-L54) the pipeline in the [infrastructure repo](https://github.com/philophilo/fire_k8s). The image is also tagged with a new version and pushed to Docker hub. The newest image tags is added as an environment variable to the infrastructure pipeline.
The pipeline requires a Circleci token used as `$CIRCLECI_TOKEN` to make api calls to the infrastucture pipeline. In Addition `$DOCKER_USERNAME` and `$DOCKER_PASSWORD` to authenticate docker to push the new image.

```
$CIRCLECI_TOKEN
$DOCKER_USERNAME
$DOCKER_PASSWORD
```