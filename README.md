# REST API for YaMDB, a review service

![example workflow](https://github.com/gtrosh/yamdb_final/actions/workflows/yamdb_workflow.yml/badge.svg)

[![Python](https://img.shields.io/badge/-Python-464646??style=flat-square&logo=Python)](https://www.python.org/)
[![Django](https://img.shields.io/badge/-Django-464646??style=flat-square&logo=Django)](https://www.djangoproject.com/)
[![Django REST Framework](https://img.shields.io/badge/-Django%20REST%20Framework-464646?style=flat-square&logo=Django%20REST%20Framework)](https://www.django-rest-framework.org/)
[![NGINX](https://img.shields.io/badge/-NGINX-464646??style=flat-square&logo=NGINX)](https://nginx.org/ru/)
[![gunicorn](https://img.shields.io/badge/-gunicorn-464646?style=flat-square&logo=gunicorn)](https://gunicorn.org/)
[![PostgreSQL](https://img.shields.io/badge/-PostgreSQL-464646??style=flat-square&logo=PostgreSQL)](https://www.postgresql.org/)
[![docker](https://img.shields.io/badge/-Docker-464646??style=flat-square&logo=docker)](https://www.docker.com/)
[![GitHub](https://img.shields.io/badge/-GitHub-464646??style=flat-square&logo=GitHub)](https://github.com/)
[![GitHub%20Actions](https://img.shields.io/badge/-GitHub%20Actions-464646??style=flat-square&logo=GitHub%20actions)](https://github.com/features/actions)
[![Yandex.Cloud](https://img.shields.io/badge/-Yandex.Cloud-464646?style=flat-square&logo=Yandex.Cloud)](https://cloud.yandex.ru/)

## About the project
This is a REST API for YaMDb - a service that contains reviews of movies, books and music titles. This was a study project (made by three developers, including me. I was responsible for models, views and endpoints for `Categories`, `Genres` and `Titles`) 

- The service collects users' `Reviews` of various `Titles`
- The titles are split by `Category` (books, movies etc.)
- Each title can be given a `Genre`
- Users write reviews about titles and give them ratings (in the range from 1 to 10, where 10 is the highest rating). The overal rating of a title is based on the average rating from all reviews.

### User roles
- *Anonymous* - can view title descriptions, read reviews and comments.
- *Authenticated user* (`user`) - has the same permissions as Anonymous; additionally, can write reviews and rate titles, can comment on other users' reviews and rate them; can edit and delete his/her reviews and comments.
- *Modetator* (`moderator`) - has the same permissions as `user`, plus can delete and edite any reviews and comments.
- *Administrator* (`admin`) - has all permissions for managing the project and its contents. Can add and remove titles, categories and genres; can assign users' roles.
- *Django Admin* has the same permissions as `admin`.

### User registration
1. User sends a POST-request with parameter *email* to `api/v1/auth/email/`.
2. The service sends the user an email with a `confirmation_code` to the email address provided in the POST-request.
3. The user sends a POST-request with paramaters of `email` and `confirmation_code` to `api/v1/auth/token/` and receives a `token` (JWT-token) in return.
These actions are required once, at registration. The user can then use the API.

## Getting ready

### 1. Clone this repository:
```bash
# create folder for your project
mkdir your-folder-name
# change to this directory:
cd your-folder-name
# clone this repository:
git clone git@github.com:gtrosh/yamdb_final.git
```
### 2. Connect to your remote server and install Docker and Docker-compose 
(note that the below instructions work for a Ubuntu server)

*Docker*
```bash
sudo apt install docker.io
```
*Docker-compose*
```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
For more information about this installation your can visit:
- Docker: https://www.docker.com/get-started
- Docker-compose: https://docs.docker.com/compose/install/

### 3. Get your remote server ready 

Copy `docker-compose.yaml` and `nginx/default.conf` from the folder on your local machine to your server. By default, `server_name` is `127.0.0.1`. You can change `nginx/default.conf` locally if you would like, before copying to the remote server.
```bash
scp docker-compose.yml '<username>@<host>:/home/<username>'/docker-compose.yml
scp nginx.conf '<username>@<host>:/home/<username>'/nginx/default.conf
```
### 4. Get your environment ready

On your remote server, create `.env` file in the project root folder and provide the following environment variables:
```bash
DB_ENGINE=django.db.backends.postgresql
DB_NAME=postgres
DB_USER=postgres
DB_PASSWORD=postgres
DB_HOST=db
DB_PORT=5432
SECRET_KEY='<secret-key-of-your-Django-project>'
DEBUG=False
```
### 5. Set up *Workflow* with GitHub *Secrets*

To be able to use *Workflow* and take advantage of *GitHub Actions*, add environment variables to GitHub *Secrets* as follows:
```bash
DB_ENGINE=django.db.backends.postgresql
DB_NAME=postgres
DB_USER=postgres
DB_PASSWORD='<your-db-password>'
DB_HOST=db
DB_PORT=5432

DOCKER_PASSWORD='<your-DockerHub-password>'
DOCKER_USERNAME='<your-DockerHub-username>'

SECRET_KEY='<secret-key-of-your-Django-project>'

USER='<username on remote server>'
HOST='<remote server IP>'
PASSPHRASE='<passphrase for your ssh key, if you had set it>'
SSH_KEY='<your SSH ключ (get get it, type the following command in your terminal: cat ~/.ssh/id_rsa)>'

TELEGRAM_TO='<chat ID for the chat that will receive the message>'
TELEGRAM_TOKEN='<your bot token>'
```
The workflow consists of four steps:
- testing the code for PEP8
- building the image and publishing it on DockerHub
- auto-deploy to a remote server
- sending notifications to a Telegram chat

### 6. Roll out the application in Docker-compose

- Start docker-compose:
```bash
sudo docker-compose up -d
```
- Apply migrations:
```bash
docker-compose exec web python manage.py makemigrations --noinput
docker-compose exec web python manage.py migrate --noinput
```
- Collect static files:
```bash
docker-compose exec web python manage.py collectstatic --noinput
```
- Create super user:
```bash
docker-compose exec web python manage.py createsuperuser
```
- (optional step) Fill database with start data"
```bash
sudo docker-compose exec web python manage.py createsuperuser
```
To check that everything went OK and the data has been added, visit http://127.0.0.1/api/v1/. To add your own data, follow http://127.0.0.1/admin

To stop the containers, go to project root directory and run the command:
```bash
sudo docker-compose stop
```
## Documentation
Documentation is available at `/static/`
Once the application has been rolled out, follow http://127.0.0.1/redoc/ to take a look.

## Rolled out app
The project is available at this [link]()

## Authors
- [Galina Troshkina](https://github.com/gtrosh) | [LinkedIn](https://www.linkedin.com/in/galina-troshkina-87683831)| [Twitter](https://twitter.com/LinaTrosh)
- [Alexander Mikhailov](https://github.com/Sasha-Mikhailov)
- [Evgeny Yakushenkov](https://github.com/Trvg51)