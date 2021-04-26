## Docs
- https://www.postgresql.org/docs/12/index.html

## Cek (ubuntu)
- postgres --version
- sudo find /usr -wholename '*/bin/postgres' --> jika tidak ditemukan version, bisa dicari directory postgres
- jika berhasil akan muncul directory, misal: /usr/lib/postgresql/12/bin/postgres
- /usr/lib/postgresql/12/bin/postgres -V ----> cek versi postgresql dari directory
- psql --version -------> cek psql console interaktif untuk postgres
- Jika postgre/psql belum ada bisa dilakukan instalasi

## ------------- Instalasi (Ubuntu) ---------------
- sudo apt update
- sudo apt install postgresql postgresql-contrib
- lakukan prosedur cek diatas
- sudo -u postgres psql ----------> masuk console psql
- jika ada masalah socket -----> sudo pg_ctlcluster 12 main start

## ---------- practice ------------
- sudo -u postgres --> user di ubuntu khusus operasi postgres, exit -> jika ingin keluar dari user ini
- createuser --interactive
- createuser -P -s -e joe --> CREATE ROLE joe PASSWORD 'md5b5f5ba1a423792b526f799ae4eb3d59e' SUPERUSER CREATEDB CREATEROLE INHERIT LOGIN;
- createdb mydb
- dropdb mydb
- psql mydb
- \h(help), \q(quit)
- TABLE
    ```sql
    CREATE TABLE weather (
        city            varchar(80),
        temp_lo         int,           
        temp_hi         int,           
        prcp            real,          
        date            date
    );
    
    DROP TABLE tablename;
    
    INSERT INTO weather (city, temp_lo, temp_hi, prcp, date)
    VALUES ('San Francisco', 43, 57, 0.0, '1994-11-29');
    
    COPY weather FROM '/home/user/weather.txt';
    
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
    
    ---- JOIN --------
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
    ```

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
