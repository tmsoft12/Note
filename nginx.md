# Nginx Barada: Giriş, Gurnamak, Sazlamalar we Buýruklar

Bu resminama, Nginx web sunucusynyň näme bolandygyny, ýerli ulgamda nädip gurnalmalydygyny, esas sazlamalary we köp ulanylýan buýruklary jikme-jik düşündirýär.

## 1. Nginx Näme?

Nginx (Engine-X diýlip okalýar), ýokary öndürijilikli, açyk kodly web sunucusydyr. Web sahypalaryny üpjün etmekden başga, ters proksi, ýük balansirleme we keşirleme ýaly funksiýalary hem ýerine ýetirýär. Nginx, ýeňilligi, az resurs sarp etmegi we köp ulanyjyly işlemek ukyby bilen meşhur.

### 1.1 Esasy Ulanylyş Ýagdaýlary

* **Web Sunucusy**: HTML, CSS, JavaScript ýaly statik faýllary we dinamik mazmuny üpjün edýär.
* **Ters Proksi**: Başga sunuculara (mysal: Go, Node.js) islegleri ugradýar.
* **Yük Balansirleme**: Trafiýi birnäçe sunuculara paýlaýar.
* **Keşirleme**: Web mazmunyny keşde saklap, çaltlaşdyryjy rol oynayýar.

## 2. Nginx-i Ýerli Ulgamda Gurnamak (Ubuntu)

Nginx-i Ubuntu-da gurnamak üçin şu ädimleri ýerine ýetiriň:

1. **Ulgamy täzelemek**:

   ```bash
   sudo apt update
   sudo apt upgrade
   ```
2. **Nginx-i gurnamak**:

   ```bash
   sudo apt install nginx
   ```
3. **Nginx-iň işleýändigini barlamak**:

   ```bash
   sudo systemctl status nginx
   ```

   Gurnamadan soň, brauzeriňizde `http://localhost` adresine girip, Nginx-iň hoş geldiň sahypasyny görüň.
4. **Nginx-i awtomatik başlatmak**:

   ```bash
   sudo systemctl enable nginx
   ```

## 3. Esasy Nginx Buýruklary

Nginx-i dolandyrmak üçin şu buýruklar ulanylýar:

* **Nginx-i başlatmak**: `sudo systemctl start nginx`
* **Nginx-i duruzmak**: `sudo systemctl stop nginx`
* **Nginx-i täzeden başlatmak**: `sudo systemctl restart nginx`
* **Sazlamalary täzeden ýüklemek** (konteýneri duruzmazdan): `sudo systemctl reload nginx`
* **Nginx-iň ýagdaýyny barlamak**: `sudo systemctl status nginx`
* **Sazlama faýlynyň dogrulygyny barlamak**: `sudo nginx -t`
* **Nginx-iň wersiýasyny görmek**: `nginx -v`

## 4. Nginx Sazlamalary

Nginx-iň sazlama faýllary adaty ýagdaýda `/etc/nginx/` katalogynda ýerleşýär. Esasy faýllar:

* **Baş faýl**: `/etc/nginx/nginx.conf` – Nginx-iň global sazlamalaryny öz içine alýar.
* **Saýt sazlamalary**: `/etc/nginx/sites-available/` we `/etc/nginx/sites-enabled/` – Her saýt üçin aýratyn sazlamalar.

### 4.1 Esasy Sazlama Faýly (`nginx.conf`)

Esasy sazlama faýlynyň gysgaça mysaly:

```nginx
user www-data;
worker_processes auto;
pid /run/nginx.pid;

events {
    worker_connections 768;
}

http {
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

* **user**: Nginx-iň işleýän ulanyjysy.
* **worker\_processes**: İşçi prosesleriň sany (adaty ýagdaýda `auto`).
* **worker\_connections**: Her prosesiň kabul edip bilýän baglanyşyk sany.
* **include**: Başga sazlama faýllaryny goşýar.

### 4.2 Saýt Sazlamasy

Ýerli ulgamda bir saýt sazlamak üçin `/etc/nginx/sites-available/` içinde täze faýl dörediň (mysal: `mysite`).

#### Mysal: Ýerli Saýt Sazlamasy

```nginx
server {
    listen 80;
    listen [::]:80;

    server_name localhost;

    root /var/www/mysite;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ /index.html;
    }

    access_log /var/log/nginx/mysite.access.log;
    error_log /var/log/nginx/mysite.error.log;
}
```

**Düşündiriş**:

* **listen**: Sunucynyň diňleýän porty (80 HTTP üçindir).
* **server\_name**: Sunucynyň ady (ýerli ulgamda `localhost`).
* **root**: Web faýllarynyň ýerleşýän ýeri.
* **index**: Adaty faýllar (mysal: `index.html`).
* **location**: Islegleriň nädip dolandyrylýandygyny görkezýär.
* **try\_files**: Sorag faýly tapylmasa, berlen ýollara seredýär.

#### Sazlamany Işjeňleşdirmek

1. Sazlama faýlyny ýerleşdiriň:

   ```bash
   sudo nano /etc/nginx/sites-available/mysite
   ```

   Ýokardaky mysaly goşuň.
2. Sazlamany işjeňleşdirmek üçin baglanyşyk dörediň:

   ```bash
   sudo ln -s /etc/nginx/sites-available/mysite /etc/nginx/sites-enabled/
   ```
3. Sazlamanyň dogrulygyny barlaň:

   ```bash
   sudo nginx -t
   ```
4. Nginx-i täzeden ýükläň:

   ```bash
   sudo systemctl reload nginx
   ```
5. Ýerli saýt üçin mazmun dörediň:

   ```bash
   sudo mkdir -p /var/www/mysite
   echo "<h1>Salam, Nginx!</h1>" | sudo tee /var/www/mysite/index.html
   ```
6. Synag: Brauzerde `http://localhost` adresine giriň. “Salam, Nginx!” ýazgysyny görersiňiz.

### 4.3 Ters Proksi Sazlamasy (Mysal)

Nginx-i ters proksi hökmünde ulanmak üçin, başga bir programma (mysal: Go API, 8080 portynda işleýär) bilen şu sazlamany ulanyp bilersiňiz:

```nginx
server {
    listen 80;
    server_name localhost;

    location / {
        proxy_pass http://localhost:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

Bu sazlama, `http://localhost` adresine gelen islegleri `http://localhost:8080` adresine ugradýar.

## 5. Ýerli Ulgamda Nginx Ulanylyşynyň Üstünlikleri

* **Çaltlyk**: Nginx, az resurs sarp edip, ýokary öndürijilik üpjün edýär.
* **Sazlama Ýönekeýligi**: Faýl esasly sazlamalar aňsat we aýdyň.
* **Synag Amatlylygy**: Ýerli ulgamda web sunucusy ýa-da proksi synag edilip bilner.

## 6. Howpsuzlyk we Maslahatlar

* **Faýl Rugsatlary**: `/var/www/` we sazlama faýllarynyň diňe zerur ulanyjylar üçin açyk bolmagyna üns beriň.
* **Ýazgylary Barlamak**: Hatalary ýüze çykarmak üçin `error_log` we `access_log` faýllaryny gözden geçiriň:
  ```bash
  sudo tail -f /var/log/nginx/error.log
  ```
* **SSL/TLS**: Ýerli ulgamda synag üçin öz-özüňden imzalyk sertifikat ulanyp bilersiňiz (real proýektlerde Let’s Encrypt ýaly ygtybarly sertifikat ulanyň).
* **Sazlama Faýlynyň Yzygiderliliği**: Her üýtgeşmeden soň `sudo nginx -t` bilen sazlamany barlaň.

## 7. Netije

Nginx, ýeňil, çalt we köp funksiýaly web sunucusydyr. Bu resminama, Nginx-i ýerli ulgamda gurnamagy, esas sazlamalary we buýruklary görkezdi. Goşmaça maglumat üçin Nginx-iň resmi resminamalaryna göz aýlap bilersiňiz.
