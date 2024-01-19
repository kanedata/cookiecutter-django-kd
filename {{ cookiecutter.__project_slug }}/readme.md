# {{ cookiecutter.project_name }}

## Initial setup

```
python -m venv env
env\Scripts\activate
pip install wheel pip-tools
pip-compile
pip-sync
```

## Setup on Dokku

```sh
# create app
dokku apps:create {{ cookiecutter.__project_slug }}

# postgres
sudo dokku plugin:install https://github.com/dokku/dokku-postgres.git postgres
dokku postgres:create {{ cookiecutter.__project_slug }}-db
dokku postgres:link {{ cookiecutter.__project_slug }}-db {{ cookiecutter.__project_slug }}

{%- if cookiecutter.include_elasticsearch -%}
# elasticsearch
sudo dokku plugin:install https://github.com/dokku/dokku-elasticsearch.git elasticsearch
dokku elasticsearch:create {{ cookiecutter.__project_slug }}-es
dokku elasticsearch:link {{ cookiecutter.__project_slug }}-es {{ cookiecutter.__project_slug }}
{%- endif -%}

# letsencrypt
sudo dokku plugin:install https://github.com/dokku/dokku-letsencrypt.git
dokku letsencrypt:set {{ cookiecutter.__project_slug }} email{{ cookiecutter.author_email }}
dokku letsencrypt:enable {{ cookiecutter.__project_slug }}
dokku letsencrypt:cron-job --add

# set secret key
# To generate use:
# `python -c "import secrets; print(secrets.token_urlsafe())"`
dokku config:set --no-restart {{ cookiecutter.__project_slug }} SECRET_KEY='<insert secret key>'

# setup account directory
dokku storage:ensure-directory {{ cookiecutter.__project_slug }}
dokku storage:mount {{ cookiecutter.__project_slug }} /var/lib/dokku/data/storage/{{ cookiecutter.__project_slug }}:/app/storage
dokku config:set {{ cookiecutter.__project_slug }} --no-restart MEDIA_ROOT=/app/storage/media/

# setup hosts
dokku config:set {{ cookiecutter.__project_slug }} --no-restart DEBUG=false ALLOWED_HOSTS="hostname.example.com"

# create superuser account
dokku run {{ cookiecutter.__project_slug }} python manage.py createsuperuser

{%- if cookiecutter.include_elasticsearch -%}
# create the elasticsearch index
python manage.py search_index --create
{%- endif -%}
```

```
git remote add dokku dokku@SERVER_HOST:{{ cookiecutter.__project_slug }}
git push dokku main
```
