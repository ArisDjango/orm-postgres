## Memchaced
[Instalation memchaced](https://github.com/memcached/memcached/wiki/Install)

## other
- sudo apt install memcached
- memcached -l 127.0.0.1:11211
- ps -ef | grep -i memc
- telnet localhost 11211
- tes:
  - stats --> untuk melihat log
  - set foo 0 3600
  - bar
  - get foo
  - maka value seharusnya adalah 'bar'
  - stats --> lihat log untuk set dan get
 
## python/django memchaced
- pip install python-memcached==1.59
- pip install django-memcache-status==2.2
- settings.py
  ```py
  CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
        'LOCATION': '127.0.0.1:11211',
        }
  }
  ```
