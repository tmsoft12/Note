# PostgreSQL Barada: Giriş, Gurnamak we Ulanylyş

Bu resminama, PostgreSQL maglumat bazasy sisteminiň näme bolandygyny, ýerli ulgamda nädip gurnalmalydygyny, esas sazlamalary we köp ulanylýan buýruklary gysgaça düşündirýär. Şeýle hem, ýönekeý bir maglumat bazasy döretmek üçin mysal berilýär.

## 1. PostgreSQL Näme?

PostgreSQL (ýa-da Postgres), açyk kodly, güýçli we ygtybarly obýekt-relaýsion maglumat bazasy dolandyryş sistemidir (RDBMS). Müräkkep soraglary, uly maglumat toplumlaryny we ýokary ulanyjyly işleri dolandyrmak ukyby bilen meşhur.

### 1.1 Esasy Aýratynlyklar

* **Relaýsion we Obýekt Goldawy**: Tablisalar, sütünler we obýekt esasly maglumat strukturlaryny goldaýar.
* **SQL Standarty**: ANSI SQL standartlaryna laýyk gelýär we goşmaça funksiýalary öz içine alýar.
* **Ygtybarlylyk**: ACID (Atomicity, Consistency, Isolation, Durability) prinsiplerini üpjün edýär.
* **Giňeldip Bolýanlyk**: JSONB, PostGIS ýaly giňeltmeler bilen funksiýalaryny giňeldip bilýär.
* **Ýerli we Bulut**: Ýerli ulgamda ýa-da AWS, Azure ýaly bulut platformalarynda işleýär.

## 2. PostgreSQL-i Ýerli Ulgamda Gurnamak (Ubuntu)

PostgreSQL-i Ubuntu-da gurnamak üçin şu ädimleri ýerine ýetiriň:

1. **Ulgamy täzelemek**:

   ```bash
   sudo apt update
   sudo apt upgrade
   ```
2. **PostgreSQL-i gurnamak**:

   ```bash
   sudo apt install postgresql postgresql-contrib
   ```
3. **PostgreSQL-iň işleýändigini barlamak**:

   ```bash
   sudo systemctl status postgresql
   ```

   Gurnamadan soň, Postgres adaty ýagdaýda `postgres` diýen ýerli ulanyjy bilen işleýär.
4. **PostgreSQL-i awtomatik başlatmak**:

   ```bash
   sudo systemctl enable postgresql
   ```
5. **Ýerli ulanyjy bilen baglanyşyk**:

   ```bash
   sudo -u postgres psql
   ```

   Bu, Postgres-iň interaktiw terminalyna (psql) girýär.

## 3. Esasy PostgreSQL Buýruklary

PostgreSQL-i dolandyrmak üçin `psql` terminalynda şu buýruklar ulanylýar:

* **Maglumat bazalaryny görmek**: `\l`
* **Tablisalary görmek**: `\dt`
* **Baglanyşyk maglumatlaryny görmek**: `\conninfo`
* **Täze maglumat bazasy döretmek**: `CREATE DATABASE <ady>;`
* **Maglumat bazasyna baglanmak**: `\c <ady>`
* **Ulanyjy döretmek**: `CREATE USER <ady> WITH PASSWORD '<parol>';`
* **Maglumat bazasyny ýok etmek**: `DROP DATABASE <ady>;`
* **psql-dan çykmak**: `\q`

Ýerli ulgamda sistem buýruklary:

* **Postgres-i başlatmak**: `sudo systemctl start postgresql`
* **Postgres-i duruzmak**: `sudo systemctl stop postgresql`
* **Postgres-i täzeden başlatmak**: `sudo systemctl restart postgresql`
* **Ýagdaýy barlamak**: `sudo systemctl status postgresql`

## 4. PostgreSQL Sazlamalary

Postgres-iň sazlama faýllary adaty ýagdaýda `/etc/postgresql/<wersiýa>/main/` katalogynda ýerleşýär. Esasy faýllar:

* **postgresql.conf**: Esasy sazlamalar (port, baglanyşyk çäklendirmeleri).
* **pg\_hba.conf**: Ulanyjy we baglanyşyk ygtyýarlyklary.

### 4.1 Esasy Sazlama Faýly (`postgresql.conf`)

Esasy sazlama faýlyny açmak üçin:

```bash
sudo nano /etc/postgresql/<wersiýa>/main/postgresql.conf
```

Mysal sazlamalar:

* **Portu üýtgetmek** (adaty ýagdaýda 5432):
  ```conf
  port = 5432
  ```
* **Maksimum baglanyşyk sany**:
  ```conf
  max_connections = 100
  ```

### 4.2 Ulanyjy Ygtyýarlyklary (`pg_hba.conf`)

Ýerli baglanyşyklary sazlamak üçin:

```bash
sudo nano /etc/postgresql/<wersiýa>/main/pg_hba.conf
```

Mysal sazlama (ýerli ulanyjylara parolsyz giriş):

```conf
local   all   postgres   peer
```

Sazlamalary ýerine ýetirmek üçin Postgres-i täzeden ýükläň:

```bash
sudo systemctl restart postgresql
```

## 5. Mysal: Ýönekeý Maglumat Bazasy Döretmek

Ýerli ulgamda bir maglumat bazasy we tablisany döredip, maglumat goşalyň.

1. **psql terminalyna girmek**:
   ```bash
   sudo -u postgres psql
   ```
2. **Täze maglumat bazasy döretmek**:
   ```sql
   CREATE DATABASE mydb;
   ```
3. **Maglumat bazasyna baglanmak**:
   ```sql
   \c mydb
   ```
4. **Tablisa döretmek**:
   ```sql
   CREATE TABLE users (
       id SERIAL PRIMARY KEY,
       name VARCHAR(100) NOT NULL,
       email VARCHAR(100) UNIQUE NOT NULL
   );
   ```
5. **Maglumat goşmak**:
   ```sql
   INSERT INTO users (name, email) VALUES ('Ahmet', 'ahmet@example.com');
   ```
6. **Maglumatlary görmek**:
   ```sql
   SELECT * FROM users;
   ```
7. **Tablisany ýok etmek** (lazym bolsa):
   ```sql
   DROP TABLE users;
   ```
8. **psql-dan çykmak**:
   ```sql
   \q
   ```

## 6. Ýerli Ulgamda PostgreSQL Ulanylyşynyň Üstünlikleri

* **Ygtybarlylyk**: ACID prinsipleri maglumatlaryň bitewiligini üpjün edýär.
* **Synag Amatlylygy**: Ýerli ulgamda programma döretmek we synag etmek aňsat.
* **Giňeldip Bolýanlyk**: JSONB ýaly özboluşly funksiýalar IoT ýa-da analitik proýektlerde peýdaly.

## 7. Howpsuzlyk we Maslahatlar

* **Parol Howpsuzlygy**: Ýerli ulgamda `postgres` ulanyjysy üçin güýçli parol goýuň:
  ```sql
  ALTER USER postgres WITH PASSWORD 'new_password';
  ```
* **Ýazgylary Barlamak**: Hatalary ýüze çykarmak üçin log faýllaryny gözden geçiriň:
  ```bash
  sudo tail -f /var/log/postgresql/postgresql-<wersiýa>-main.log
  ```
* **Baglanyşyk Çäklendirmeleri**: `pg_hba.conf` faýlynda ýerli we uzaak baglanyşyklary ygtyýarlyklaryny sazlaň.
* **Ygtyýarlandyrylan Ulanyjylar**: Her programma üçin aýratyn ulanyjy we parol dörediň.

## 8. Netije

PostgreSQL, ygtybarly, giňeldip bolýan we ýokary öndürijilikli maglumat bazasy sistemidir. Bu resminama, Postgres-i ýerli ulgamda gurnamagy, esas sazlamalary we ýönekeý bir maglumat bazasy bilen işlemek ädimlerini görkezdi. Goşmaça maglumat üçin PostgreSQL-iň resmi resminamalaryna göz aýlap bilersiňiz.
