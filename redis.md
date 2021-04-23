## Redis
- Instalasi --> https://redis.io/topics/quickstart
<a name="C41"></a>
- C.4.1. Installing Redis
    - Linux
      - download the latest Redis version from https://redis.io/download
        - Unzip the tar.gz file, enter the redis directory, and compile Redis using the make command, as follows:
            ```sh
            cd redis-5.0.8
            make
            ```
    - Windows Subsystem for Linux (WSL)
      - You can read instructions on enabling WSL and installing Redis at https://redislabs.com/blog/redis-on-windows-10/
    - After installing Redis, use the following shell command to start the Redis server:
        ```
        src/redis-server
        ```
        ```
        By default, Redis runs on port 6379.
        you can specify a custom port using the --port flag, for example, redis-server --port 6655.

        ```
    - Keep the Redis server running and open another shell. Start the Redis client with the following command:
        ```
        src/redis-cli

        You will see the Redis client shell prompt, like this:
        127.0.0.1:6379>
        ```
    - Enter the SET command in the Redis shell to store a value in a key:
        ```
        127.0.0.1:6379> SET name "Peter"
        OK
        ```
        ```
        Note:
        - The preceding command creates a name key with the string value "Peter" in the Redis database.
        - The OK output indicates that the key has been saved successfully.
        ```
    - Next, retrieve the value using the GET command, as follows:
        ```
        127.0.0.1:6379> GET name
        "Peter" 
        ```
    - You can also check whether a key exists using the EXISTS command. This command returns 1 if the given key exists, and 0 otherwise:
        ```
        127.0.0.1:6379> EXISTS name
        (integer) 1
        ```
    - You can set the time for a key to expire using the EXPIRE command, which allows you to set time-to-live in seconds.
        ```
        127.0.0.1:6379> GET name
        "Peter"
        127.0.0.1:6379> EXPIRE name 2
        (integer) 1

        Wait for two seconds and try to get the same key again:
        127.0.0.1:6379> GET name
        (nil)
            * The (nil) response is a null response and means that no key has been found.
        ``` 
    - You can also delete any key using the DEL command, as follows:
        ```
        127.0.0.1:6379> SET total 1
        OK
        127.0.0.1:6379> DEL total
        (integer) 1
        127.0.0.1:6379> GET total
        (nil)
        ```
    - These are just basic commands for key operations.
    - Redis commands at https://redis.io/commands
    - Redis data types at https://redis.io/topics/data-types

<a name="C42"></a>
- Using Redis with Python
    - Docs: https://redis-py.readthedocs.io/
    - pip install redis==3.4.1
    - python manage.py shell --> execute the following code:
        ```
        >>> import redis
        >>> r = redis.Redis(host='localhost', port=6379, db=0)
        ```
        ```
        Note: 
        - The preceding code creates a connection with the Redis database.
        - In Redis, databases are identified by an integer index instead of a database name. By default, a client is connected to the database 0.
        - The number of available Redis databases is set to 16, but you can change this in the redis.conf configuration file.
        ```
    - Next, set a key using the Python shell:
        ```
        >>> r.set('foo', 'bar')
        True
        ```
        ```
        Note:
        - The command returns True, indicating that the key has been successfully created.
    - Now you can retrieve the key using the get() command:
        ```
        >>> r.get('foo')
        b'bar'
        ```
        ```
        As you will note from the preceding code, the methods of Redis follow the Redis command syntax.
        ```
    - Let's integrate Redis into your project.
      - Edit bookmarks/settings.py
        ```py
        REDIS_HOST = 'localhost'
        REDIS_PORT = 6379
        REDIS_DB = 0
        ```
<a name="C43"></a>
- Storing item views in Redis
    - Tujuan:
        - Let's find a way to store the total number of times an image has been viewed.
        - If you implement this using the Django ORM, it will involve a SQL UPDATE query every time an image is displayed.
        - If you use Redis instead, you just need to increment a counter stored in memory, resulting in a much better performance and less overhead.
    - Edit images/views.py
        - add the following code to it after the existing import statements:

            ```py
            import redis
            from django.conf import settings

            # connect to redis
            r = redis.Redis(host=settings.REDIS_HOST,
                            port=settings.REDIS_PORT,
                            db=settings.REDIS_DB)

            ```
        - modify the `image_detail` view, like this:

            ```py

            def image_detail(request, id, slug):
                image = get_object_or_404(Image, id=id, slug=slug)
                # increment total image views by 1
                total_views = r.incr(f'image:{image.id}:views')
                return render(request,
                                'images/image/detail.html',
                                {'section': 'images', 'image': image, 'total_views': total_views }
                            )
            ```
            ```
            Note:
            - In this view, you use the incr command that increments the value of a given key by 1. If the key doesn't exist, the incr command creates it.
            - The incr() method returns the final value of the key after performing the operation.
            - You store the value in the total_views variable and pass it in the template context.
            - You build the Redis key using a notation, such as object-type:id:field (for example, image:33:id).
            ```
            ```
            " The convention for naming Redis keys is to use a colon sign as a separator for creating namespaced keys.
            By doing so, the key names are especially verbose and related keys share part of the same schema in their names.
            ```
        - Edit templates/images/image/detail.html
            - add the following code to it after the existing `<span class="count">` element:

                ```html
                <span class="count">
                {{ total_views }} view{{ total_views|pluralize }}
                </span>
                ```
        - Run:
            - open an image detail page in your browser and reload it several times.
            - You will see that each time the view is processed, the total views displayed is incremented by 1.
            - You have successfully integrated Redis into your project to store item counts.
