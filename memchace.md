## Memchaced
[Instalation memchaced](https://github.com/memcached/memcached/wiki/Install)

## other instalation
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
- instalasi/setting
  - pip install python-memcached==1.59
  - settings.py
    ```py
    CACHES = {
      'default': {
          'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
          'LOCATION': '127.0.0.1:11211',
          }
    }
    ```
- Monitoring status chache
  - pip install django-memcache-status==2.2 --> settings app registration 'memcache_status', 
  - add cache status di admin
  - course/admin.py
  ```py
  # use memcache admin index site
  admin.site.index_template = 'memcache_status/admin_index.html'
  ```
  - pada localhot/admin akan muncul tab monitor cache

## Using the low-level cache API
- Mencoba praktek operasi memchaced di python/django
  - python manage.py shell
  - `from django.core.cache import cache`
  - `cache.set('musician', 'Django Reinhardt', 20)` --> Note: set() --> untuk set chache, key='musician', timeout=20 seconds
  - `cache.get('musician')` --> value = 'Django Reinhardt', jika get() digunakan setelah 20 s, maka value=none, jika timeout tidak di set, maka akan mengambil dari conf
- Mencoba memchache dengan queryset
  - python manage.py shell
  ```py
  from courses.models import Subject
  
  subjects = Subject.objects.all()
  cache.set('my_subjects', subjects)
  cache.get('my_subjects')
  ```
## Caching based on dynamic data
- contoh:
  ```py
  class CourseListView(TemplateResponseMixin, View):
      model = Course
      template_name = 'courses/course/list.html'

      def get(self, request, subject=None):
          # subjects = Subject.objects.annotate(total_courses=Count('courses')) # diganti memcache
          subjects = cache.get('all_subjects')
          if not subjects:
              subjects = Subject.objects.annotate(total_courses=Count('courses'))
              cache.set('all_subjects', subjects)
          # courses = Course.objects.annotate(total_modules=Count('modules')) # diganti memcached -- all_courses
          all_courses = Course.objects.annotate(total_modules=Count('modules'))
          if subject:
              subject = get_object_or_404(Subject, slug=subject)
              # courses = courses.filter(subject=subject)
              key = f'subject_{subject.id}_courses'
              courses = cache.get(key)
              if not courses:
                  courses = all_courses.filter(subject=subject)
                  cache.set(key, courses)
          else:
              courses = cache.get('all_courses')
              if not courses:
                  courses = all_courses
                  cache.set('all_courses', courses)

          return self.render_to_response({'subjects': subjects,
                                          'subject': subject,
                                          'courses': courses})
  ```
  
## Caching template fragments
