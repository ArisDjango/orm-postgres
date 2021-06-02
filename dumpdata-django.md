## dumpdata django
- Installasi/clone
  - Clone git
  - python -m venv venv --> `python venv/bin/activate`
  - install dependency --> `pip install -r requirements.txt`
  - Pastikan ada usernya, atau buat superuser baru --> `python manage.py createsuperuser`
- `python manage.py dumpdata --help`
- `python manage.py dumpdata courses --indent=2` --> output di console
- `mkdir courses/fixtures`
- `python manage.py dumpdata courses --indent=2 --output=courses/fixtures/subjects.json`
- `python manage.py loaddata subjects.json`
```
You can read about how to use fixtures for testing at https://docs.djangoproject.com/en/3.0/topics/testing/tools/#fixture-loading.
If you want to load fixtures in model migrations, take a look at Django's documentation about data migrations. You can find the documentation for migrating data at
https://docs.djangoproject.com/en/3.0/topics/migrations/#datamigrations.
```
