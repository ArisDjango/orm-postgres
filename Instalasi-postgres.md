## Docs
- https://www.postgresql.org/docs/12/index.html
- https://ubuntu.com/server/docs/databases-postgresql

## Cek (ubuntu)
- `ps -ef | grep postgresql` --> cek service run/tidak
- `sudo service postgresql start`
- `sudo service postgresql stop`
- `postgres --version`
- sudo find /usr -wholename '*/bin/postgres' --> jika tidak ditemukan version, bisa dicari directory postgres
- jika berhasil akan muncul directory, misal: /usr/lib/postgresql/12/bin/postgres
- `/usr/lib/postgresql/12/bin/postgres -V` ----> cek versi postgresql dari directory
- `psql --version` -------> cek psql console interaktif untuk postgres
- Jika postgre/psql belum ada bisa dilakukan instalasi

## ------------- Instalasi (Ubuntu) ---------------
- `sudo apt update`
- `sudo apt install postgresql postgresql-contrib`
- lakukan prosedur cek diatas
- `sudo -u postgres psql` ----------> masuk console psql sebagai super user
- 
- Masalah:
    - `sudo pg_ctlcluster 12 main start`  -----> jika ada masalah socket
    - `sudo nano /etc/postgresql/12/main/pg_hba.conf` `sudo pg_ctlcluster 12 main reload` --> user tidak bisa menjalankan psql dari home --> rubah local dari peer ke md5,
- Masalah (centos)
    - mencari hba.conf
    - `sudo -u postgres` --> `SHOW config_file;` --> /var/lib/pgsql/data/postgresql.conf
    - `sudo su` --> `nano /var/lib/pgsql/data/pg_hba.conf` --> ubah user ke md5
    -  `sudo rm -fr /var/lib/pgsql/data/*` --> jika service tidak mau jalan

## ---------- Instalasi RHEL/Centos ----------
- https://techviewleo.com/install-postgresql-12-on-amazon-linux/
- https://developpaper.com/install-configure-postgresql-12-on-centos-7/
- https://www.hostinger.com/tutorials/how-to-install-postgresql-on-centos-7/
- [Error instalasi psycopg2, gcc, devel](https://stackoverflow.com/questions/41611551/python-cant-install-psycopg2-on-centos-7)

## ---------- Base ------------
- sudo su --> login sebagai root
- su - postgres --> login sebagai superuser postgres
- createuser --interactive
- createuser dP educa --> pake password
- createuser -P -s -e joe --> CREATE ROLE joe PASSWORD 'md5b5f5ba1a423792b526f799ae4eb3d59e' SUPERUSER CREATEDB CREATEROLE INHERIT LOGIN;
- createdb mydb
- dropdb mydb


## --- Manage Dtabase ---
- sudo su --> login sebagai root
- su - postgres --> login sebagai superuser postgres
- psql --> masuk shell
- \h(help), \q(quit), \l, \du, \dt
    ```sql
    CREATE USER user_name WITH ENCRYPTED PASSWORD 'mypassword';
    
    CREATE ROLE user_name WITH SUPERUSER CREATEROLE CREATEDB; --> memberikan role
    ALTER ROLE user_name CREATEDB; --> merubah role, = alter user
    ALTER ROLE miriam SUPERUSER CREATEROLE CREATEDB;
    
    CREATE DATABASE dbname
    CREATE DATABASE dbname OWNER rolename;
    CREATE DATABASE dbname TEMPLATE template0;
    
    GRANT READ ON dbname TO manuel;
    GRANT ALL PRIVILEGES ON dbname TO manuel;
    
    ALTER DATABASE mydb SET geqo TO off; --> disable
    
    DROP DATABASE name;
    DROP ROLE jonathan; --> = drop user
    
    CREATE TABLESPACE fastspace LOCATION '/ssd1/postgresql/data';
    ```
## ---- QUERY ----
- Basic Query
    ```sql
    psql weatherdb
    --- Create Table with column ---
    CREATE TABLE weather (
        city            varchar(80),
        temp_lo         int,           
        temp_hi         int,           
        prcp            real,          
        date            date
    );
    
    --- Update/insert new column ---
    ALTER TABLE weather 
        ADD count int,
        ADD name varchar(80);
    
    --- Delete Table ---
    DROP TABLE tablename;
    
    
    
    ---- Populate -------
    INSERT INTO weather (city, temp_lo, temp_hi, prcp, date)
    VALUES ('San Francisco', 43, 57, 0.0, '1994-11-29');
    
    COPY weather FROM '/home/user/weather.txt';
    
    
    --- Query Table -----
    SELECT * FROM weather;
    SELECT city, temp_lo, temp_hi, prcp, date FROM weather;
    SELECT city, (temp_hi+temp_lo)/2 AS temp_avg, date FROM weather;
    
    SELECT * FROM weather
        WHERE city = 'San Francisco' AND prcp > 0.0;
        
    SELECT * FROM weather
        ORDER BY city, temp_lo;
        
    SELECT DISTINCT city
        FROM weather;
    
    SELECT DISTINCT city
        FROM weather
        ORDER BY city;
        
        
    
    ---- Join Table --------
    SELECT *
        FROM weather, cities
        WHERE city = name;
    
    SELECT *
        FROM weather INNER JOIN cities ON (weather.city = cities.name);
        
    SELECT *
        FROM weather LEFT OUTER JOIN cities ON (weather.city = cities.name);
        
    SELECT W1.city, W1.temp_lo AS low, W1.temp_hi AS high,
        W2.city, W2.temp_lo AS low, W2.temp_hi AS high
        FROM weather W1, weather W2
        WHERE W1.temp_lo < W2.temp_lo
        AND W1.temp_hi > W2.temp_hi;
        
    ---- Agreggate Function ----
    SELECT max(temp_lo) FROM weather;
    
    SELECT city FROM weather
        WHERE temp_lo = (SELECT max(temp_lo) FROM weather);
        
    SELECT city, max(temp_lo)
        FROM weather
        GROUP BY city;
        
    SELECT city, max(temp_lo)
        FROM weather
        WHERE city LIKE 'S%'
        GROUP BY city
        HAVING max(temp_lo) < 40;
        
        
    --- Updates -----
    UPDATE weather
        SET temp_hi = temp_hi - 2,  temp_lo = temp_lo - 2
        WHERE date > '1994-11-28';
    
    
    --- Delete ----
    DELETE FROM weather WHERE city = 'Hayward';
    
    ```
- Advanced Query
    ```sql
    --- Views ---------
    CREATE VIEW myview AS
    SELECT city, temp_lo, temp_hi, prcp, date, location
        FROM weather, cities
        WHERE city = name;

    SELECT * FROM myview;
    
    
    ---- Foreign Keys ----------
    CREATE TABLE cities (
        city     varchar(80) primary key,
        location point
    );

    CREATE TABLE weather (
            city      varchar(80) references cities(city),
            temp_lo   int,
            temp_hi   int,
            prcp      real,
            date      date
    );
    
    ```
- Backup - Restore
    ```sql
    pg_dump dbname > dumpfile --> backup
    psql dbname < dumpfile ---> restore
    
    cat filename.gz | gunzip | psql dbname --> file zip
    
    ```
- Monitoring
    ```
    ps auxww | grep ^postgres
    psql -c 'SHOW cluster_name'
    ```
- Postgre Server
    ```
    root# mkdir /usr/local/pgsql
    root# chown postgres /usr/local/pgsql
    root# su postgres
    postgres$ initdb -D /usr/local/pgsql/data
    ```
- https://www.postgresql.org/docs/12/sql.html ---> part 2
- 
    
## -------------- Instalasi (windows)-----------------------
- install postgresql https://www.postgresql.org/
- pastikan system32 dan folder instalasi /bin masuk di path environment
- (disini biasanya muncul masalah, lihat di bab problem)
- Pengelolaan db juga bisa melakui pgadmin

## ----------- Practice ---------------------------
- psql -U postgres -h localhost --> login sebagai superuser
    - Membuat User/pass baru:
        -  CREATE USER aris;
        -  ALTER USER aris PASSWORD 'aris1985';
    - Memberikan hak akses membuat db:
        -  ALTER USER aris CREATEDB;
    - CREATE DATABASE data_karyawan;
    - Memberikan hak akses read/write/delete/update ke db tertentu --> jika dbase dibuat oleh uspersuer dan agar user biasa bisa akses
        -  GRANT ALL ON DATABASE data_karyawan to "aris";
    - cek data user yang aktif --> \du
    - keluar --> \quit

##---------- Perintah -------------------
- daftar table pg_database --> https://www.postgresql.org/docs/9.4/catalog-pg-database.html

- \c <database> <user> , ex: \c blog_baru aris   ---> connect antar user dan database

- List daftar database
    - \l --> list semua database
    - list dbase berdasarkan user 33235 --> SELECT datname FROM pg_database where datdba=33235;
- List daftar user
    - \du --> data user all list
- \dt --> data table all list



## ----------- User ----------------------------------
- Membuat DB baru by user 'aris'
   - createdb -U aris data_karyawan;
- Akses DB by user 'aris'
    - psql -U aris data_karyawan
- Membuat table baru
    ```
    CREATE TABLE accounts (
        user_id serial PRIMARY KEY,
        username VARCHAR ( 50 ) UNIQUE NOT NULL,
        password VARCHAR ( 50 ) NOT NULL,
        email VARCHAR ( 255 ) UNIQUE NOT NULL,
        created_on TIMESTAMP NOT NULL,
            last_login TIMESTAMP 
    );
    ```


    
    





bisa dimulai memasukkan table ke database atau melalui ORM django
