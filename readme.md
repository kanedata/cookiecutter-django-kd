# Cookiecutter Django - Kane Data Limited

A minimal Django app for Kane Data Limited, to generate websites using [cookiecutter](https://cookiecutter.readthedocs.io/).

Includes:

 - Github actions for linting and testing
 - Autogenerated secret key
 - Jinja2 Templating (with common utilities)
 - Django HTMX
 - 12 Factor settings in a .env file
 - Static storage with Whitenoise
 - Media storage with AWS/DigitalOcean spaces
 - Django Debug Toolbar

## Usage

To generate an app use:

```
cookiecutter https://github.com/kanedata/cookiecutter-django-kd
```

And follow the prompts.