# django-tricks
Some tricks for django developing


## Auto disocver app models

You may have lot of models defined in your `models.py` fields, and want to leverage the admin site to help you manage yor models.
But it need repeated work to define every models in your `admin.py`, to let admin dicover the models.

You can now use the following scripts to auto disover your models:

```python
# <YOUR PROJECT>/<YOUR APP>/admin.py
from django.db import models as md
from django.contrib import admin

from backend import models


def auto_discover_models(app_models):
    """Auto discover the DB models in your app models module
    
    :param app_models: Your app models module
    """
    for k, v in app_models.__dict__.items():
        if issubclass(v.__class__, md.base.ModelBase) and \
           v.__module__ == app_models.__name__:
            model_admin_cls = type(v.__name__, (admin.ModelAdmin, ), {})
            try:
                admin.register(v)(model_admin_cls)
            except Exception:
                pass


auto_discover_models(models)
```

If you need to customized some models, let's say, `User`, `Book`, and let other models to be auto discoverred, you can also using the above scripts to achieve your goal.

```python
# <YOUR PROJECT>/<YOUR APP>/admin.py
from django.db import models as md
from django.contrib import admin

from backend import models


@admin.register(models.User)
class User(admin.ModelAdmin):
    list_display = ('username', 'id')


def auto_discover_models(app_models):
    """Auto discover the DB models in your app models module
    
    :param app_models: Your app models module
    """
    for k, v in app_models.__dict__.items():
        if issubclass(v.__class__, md.base.ModelBase) and \
           v.__module__ == app_models.__name__:
            model_admin_cls = type(v.__name__, (admin.ModelAdmin, ), {})
            try:
                admin.register(v)(model_admin_cls)
            except Exception:
                pass


auto_discover_models(models)
```
