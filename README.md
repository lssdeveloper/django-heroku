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

## Configuring the Data Base
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

# Extra places for collectstatic to find static files.
* STATICFILES_DIRS = (
    os.path.join(BASE_DIR, 'static'),
)

* STATIC_URL = '/static/'

* STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'


## Create a requirements-dev.txt
pip freeze > requirements-dev.txt

*obs:Retirar gunicorn e psycopg2==2.7.4

## Instalar o Whitenoise como indicado no link abaixo devido a problemas com arquivos estáticos

* https://devcenter.heroku.com/articles/django-assets
* Instalar o whitenoise==3.3.1 e incluir no Settings.py

*STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'


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
* heroku apps:create app-name (app-name = nome da aplicação DNS) 
* Copiar os https gerados no Terminal para futura oportunidade
https://app-name.herokuapp.com/ | https://git.heroku.com/app-name.git

## Setting the allowed hosts
* Inclua seu endereço nas diretivas ALLOWED_HOSTS em settings.py - Apenas o domínio, certifique-se de retirar o protocolo https://
* EX in settings.py :ALLOWED_HOSTS = ['domain.herokuapp.com']
* OBS: em DEV coloque no settings assim sempre que for desenvolver depois do deploy:
* ALLOWED_HOSTS = ['nome-da-app.herokuapp.com', 'localhost', '127.0.0.1']

* OBS: Sempre em DEV coloque o .env DEBUG=True e no Heroku altere para False pois em DEV ocorre o erro de BAD REQUEST 500 ()

## Heroku install config plugin
* heroku plugins:install heroku-config

### Sending configs from .env to Heroku ( Este aquivo .env deve estar no diretório raiz do projeto)
* heroku config:push

### To show heroku configs do
* heroku config 

## Publishing the app
* git add .
* git commit -m 'Configuring the app'
* OBS: Faça o Deploy com o Debug=True no .env pois senão recebera'o erro abaixo:
* git push heroku master --force

* Collectstatic error while deploying Django app to Heroku
* Error while running '$ python manage.py collectstatic --noinput'. in heroku
* solução: $ heroku config:set DISABLE_COLLECTSTATIC=1

## Creating the data base
* heroku run python3 manage.py migrate

## Creating the Django admin user
* heroku run python3 manage.py createsuperuser

## EXTRAS
### You may need to disable the collectstatic
* heroku config:set DISABLE_COLLECTSTATIC=1

### Changing a specific configuration
* heroku config:set DEBUG=True

