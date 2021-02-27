# docker-django-react-postgres-nginx

#### About Project

The project is a complete example of boilerplate project. This project uses:
* Django
* Gunicorn
* React.js
* Postgres
* Nginx
* Docker
* Docker Compose

There are two environments defined here. Development and production.


## Development Environment

#### About

This environment uses three docker containers. `backend` django app, `frontend` react app, `db` postgres database.

### Setup

Use `.env.sample` file to create `.env.dev` file. It can look like this:
```
DEBUG=1
SECRET_KEY=foo
DJANGO_ALLOWED_HOSTS=localhost 127.0.0.1 [::1]
DJANGO_SETTINGS_MODULE=app.settings.local

SQL_ENGINE=django.db.backends.postgresql
SQL_DATABASE=django_dev
SQL_USER=django
SQL_PASSWORD=django
SQL_HOST=db
SQL_PORT=5432
DATABASE=postgres
```

### Run

Build the application:

```docker-compose build```

Start application:

```docker-compose up```

or to run it in the background

```docker-compose up -d```

Now you can visit [localhost:3000/](http://localhost:3000/) to see your frontend app running
and backend app at port `8000` so admin panel is here [localhost:8000/api/admin/](http://localhost:8000/api/admin/)

to stop it:

```docker-compose down```



### Stand alone apps

You can run apps separetely without using docker. To do so for frontend app:

```
cd frontend/
npm install
npm start
```
and for backend django app just follow:
```
cd backend/
source env/bin/activate 
pip install -r requirements/local.txt
python manage.py migrate
python manage.py runserver
```
by default this approach will use sqlite3 database

## Production Environment

#### About

This environment is designed to be used in production setup. 

Backend app for this project uses multistage build. First is creating wheel for packages to be installed later. Second step is creating coping files/folders etc. It installs whell packages and runs entrypoint script. Entrypoint runs collectstatic for backend static files (to be used for django admin panel) and applies migrations. Gunicorn is used for django.

Postgres database is used.

Frontend application is also a multistage build. First node container creates optimized static files. Then nginx container serves them. It also redirects requests for backed service (`/api`)

### Setup

Create `.env.prod` file (similar to `.env.sample` file) eg:
```
DEBUG=0
SECRET_KEY=TODO-changeme
DJANGO_ALLOWED_HOSTS=localhost 127.0.0.1 [::1] your-domain-here
DJANGO_SETTINGS_MODULE=app.settings.production

SQL_ENGINE=django.db.backends.postgresql
SQL_DATABASE=django_prod
SQL_USER=django
SQL_PASSWORD=django
SQL_HOST=db-prod
SQL_PORT=5432
DATABASE=postgres

```

Create `.env.prod.db` file and fill it with:

```
POSTGRES_USER=django
POSTGRES_PASSWORD=django
POSTGRES_DB=django_prod
```
those of course need to match variables from previous file. 

### Run

Build the application:

```docker-compose -f docker-compose.prod.yml build```

Start application:

```docker-compose -f docker-compose.prod.yml up```

or to run it in the background

```docker-compose -f docker-compose.prod.yml up -d```

In your browser you can visit [localhost](http://localhost) to see app running.

Admin panel can be found at [localhost/api/admin/](http://localhost/api/admin/)

to stop it:

```docker-compose -f docker-compose.prod.yml down```
