# django_db
* pip install django_db
* A simple way to use django orm not in a classic django project
* Multi databases support
* Replace mysqldb with pymysql, so it will work with gevent.
  Use monkey patch before you import  django_db.
* If you use gevent with mysql, it will create one connection per greelet. So remember to control connection number.
  Db operation workers pool is a great way.
  Close connection manually with django.db.connections.close_all() after each request is another way but not recommended.


## simple example

```python
from django.db import models
from django_db import add_db, create_model, sync_table

# django db configure
db_config = {
    'ENGINE': 'django.db.backends.mysql',
    'HOST': '127.0.0.1',
    'PORT': '3306',
    'NAME': 'test',
    'USER': 'test',
    'PASSWORD': 'test',
    'CONN_MAX_AGE': 0,
}

_app_lable = add_db(db_config)


class Test(models.Model):
    name = models.CharField(max_length=32)

    class Meta:
        app_label = _app_lable
        db_table = 'test'


if __name__ == '__main__':
    create_model(Test)
    Test.objects.create(id=1, name='test')
```

## multi databases example

```python
_app_lable = add_db(db_config)
_app_lable2 = add_db(db_config2)


class Test(models.Model):
    name = models.CharField(max_length=32)

    class Meta:
        app_label = _app_lable
        db_table = 'test'


class Test2(models.Model):
    name = models.CharField(max_length=32)

    class Meta:
        app_label = _app_lable2
        db_table = 'test2'

if __name__ == '__main__':
    create_model(Test1)
    sync_table(Test2)
    Test.objects.create(id=1, name='test')
    Test.objects.create(id=1, name='test2')
```

## test
```sh
python -m tests.run
```