# django-heroku
Minimal configuration to host a Django project at Heroku

## Create the project directory
* mkdir directory_name
* cd directory_name

## Create and activate your virtuanenv
* virtualenv -p python3 .vEnv
* . .vEnv/bin/activate

## Installing django
* pip install django

## Create the django project
* django-admin startproject myproject

## Creating the Git repository
* git init 
* Create a file called `.gitignore` with the following content:
```
# See the name for you IDE
.idea
# If you are using sqlite3
*.sqlite3
# Name of your virtuan env
.vEnv
*pyc
```
* git add .
* git commit -m 'First commit'

## Hidding instance configuration
* pip install python-decouple
* create an .env file at the root path and insert the following variables
- SECRET_KEY=Your$eCretKeyHere (Get this secrety key from the settings.py)
- DEBUG=True  (OBS: Atenção pois não deve haver espaço entre o sinal de igual) 

### Settings.py
* from decouple import config
* SECRET_KEY = config('SECRET_KEY')
* DEBUG = config('DEBUG', default=False, cast=bool)

## Configuring the Daba Base
* pip install dj-database-url

### Settings.py
* from dj_database_url import parse as dburl

default_dburl = 'sqlite:///' + os.path.join(BASE_DIR, 'db.sqlite3')

DATABASES = {
    'default': config('DATABASE_URL', default=default_dburl, cast=dburl),
}


## Static files 
pip install dj-static

### wsgi
* from dj_static import Cling
* application = Cling(get_wsgi_application())

### Settings.py
* STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')

## Create a requirements-dev.txt
pip freeze > requirements-dev.txt

## Create a file requirements.txt file and include reference to previows file and add two more requirements
* -r requirements-dev.txt
* gunicorn
* psycopg2 (Obs: Este deve estar instalado na máquina antes de gerar o requirements.txt)

## Create a file Procfile and add the following code
* web: gunicorn nome_da_aplicacao.wsgi --log-file -
EX: web: gunicorn gestao_clientes.wsgi --log-file -

Senão um erro ocorrerá no log do Heroku:
ModuleNotFoundError: No module named 'website'

## Create a file runtime.txt and add the following core
* python-3.6.0

## Creating the app at Heroku
You should install heroku CLI tools in your computer previously ( See http://bit.ly/2jCgJYW ) 
* heroku apps:create app-name
Remember to grab the address of the app in this point

## Setting the allowed hosts
* include your address at the ALLOWED_HOSTS directives in settings.py - Just the domain, make sure that you will take the protocol and slashes from the string
EX in settings.py :ALLOWED_HOSTS = ['domain.herokuapp.com']
OBS: em DEV coloque no settings assim sempre que for desenvolver depois do deploy:
ALLOWED_HOSTS = ['nome-da-app.herokuapp.com', 'localhost']

## Heroku install config plugin
* heroku plugins:install heroku-config

### Sending configs from .env to Heroku ( Este aquivo .env deve estar no diretório raiz do projeto)
* heroku config:push

### To show heroku configs do
* heroku config 

## Publishing the app
* git add .
* git commit -m 'Configuring the app'
* git push heroku master --force

OBS:Collectstatic error while deploying Django app to Heroku
Error while running '$ python manage.py collectstatic --noinput'. in heroku
solução: $ heroku config:set DISABLE_COLLECTSTATIC=1

## Creating the data base
* heroku run python3 manage.py migrate

## Creating the Django admin user
* heroku run python3 manage.py createsuperuser

## EXTRAS
### You may need to disable the collectstatic
* heroku config:set DISABLE_COLLECTSTATIC=1

### Changing a specific configuration
* heroku config:set DEBUG=True

