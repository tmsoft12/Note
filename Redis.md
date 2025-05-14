# Redis Barada: Giriş, Gurnamak we Ulanylyş

Bu resminama, Redis maglumat bazasy sisteminiň näme bolandygyny, ýerli ulgamda nädip gurnalmalydygyny, esas sazlamalary we köp ulanylýan buýruklary gysgaça düşündirýär. Şeýle hem, ýönekeý bir ulanylyş mysaly berilýär.

## 1. Redis Näme?

Redis (Remote Dictionary Server), açyk kodly, ýatda saklanýan (in-memory), ýokary öndürijilikli maglumat bazasy we keşirleme sistemidir. Anahtar-deger (key-value) strukturasy esasynda işleýär we ýönekeý, çalt maglumat dolandyryşy üçin ulanylýär.

### 1.1 Esasy Aýratynlyklar

* **Ýatda Saklama**: Maglumatlar ýatda (RAM) saklanylýar, bu bolsa çalt okamak we ýazmak ukybyny üpjün edýär.
* **Maglumat Strukturalary**: String, list, set, hash we sorted set ýaly dürli maglumat tiplerini goldaýar.
* **Keşirleme**: Web programmalarynda çaltlaşdyryjy keş hökmünde ulanylýar.
* **Yzygiderlilik**: Maglumatlary diskde saklamak üçin RDB we AOF (Append-Only File) usullaryny goldaýar.
* **Pub/Sub**: Habarlaşyk üçin publish/subscribe modelini üpjün edýär.

### 1.2 Ulanylyş Ýagdaýlary

* Keşirleme (mysal: web sahypalarynyň mazmuny).
* Seans (session) dolandyryşy.
* Real wagt analitikalar (mysal: lider tablisasyny dolandyrmak).
* Habarlaşyk (queue) sistemleri.

## 2. Redis-i Ýerli Ulgamda Gurnamak (Ubuntu)

Redis-i Ubuntu-da gurnamak üçin şu ädimleri ýerine ýetiriň:

1. **Ulgamy täzelemek**:

   ```bash
   sudo apt update
   sudo apt upgrade
   ```
2. **Redis-i gurnamak**:

   ```bash
   sudo apt install redis-server
   ```
3. **Redis-iň işleýändigini barlamak**:

   ```bash
   sudo systemctl status redis
   ```

   Gurnamadan soň, Redis adaty ýagdaýda `localhost:6379` adresinde işleýär.
4. **Redis-i awtomatik başlatmak**:

   ```bash
   sudo systemctl enable redis
   ```
5. **Redis CLI bilen baglanyşyk**:

   ```bash
   redis-cli
   ```

   Bu, Redis-iň interaktiw terminalyna girýär. Mysal buýruk: `PING` (çykyş: `PONG`).

## 3. Esasy Redis Buýruklary

Redis-i dolandyrmak üçin `redis-cli` terminalynda şu buýruklar ulanylýar:

### 3.1 String Buýruklary

* **Maglumat goşmak**: `SET key value` (mysal: `SET name Ahmet`)
* **Maglumat almak**: `GET key` (mysal: `GET name`)
* **Maglumat ýok etmek**: `DEL key` (mysal: `DEL name`)
* **Bütin maglumatlary ýok etmek**: `FLUSHALL`

### 3.2 List Buýruklary

* **Sola goşmak**: `LPUSH list_key value` (mysal: `LPUSH tasks "Task 1"`)
* **Sağa goşmak**: `RPUSH list_key value`
* **Listden almak**: `LRANGE list_key start end` (mysal: `LRANGE tasks 0 -1`)

### 3.3 Set Buýruklary

* **Sete goşmak**: `SADD set_key value` (mysal: `SADD users Ahmet`)
* **Setiň elementlerini görmek**: `SMEMBERS set_key`

### 3.4 Hash Buýruklary

* **Hash goşmak**: `HSET hash_key field value` (mysal: `HSET user:1 name Ahmet`)
* **Hashdan almak**: `HGET hash_key field` (mysal: `HGET user:1 name`)

### 3.5 Umumy Buýruklar

* **Anahtarlar görmek**: `KEYS *`
* **Maglumat bazasyny arassalamak**: `FLUSHDB`
* **Redis-iň ýagdaýyny barlamak**: `INFO`

## 4. Redis Sazlamalary

Redis-iň sazlama faýly adaty ýagdaýda `/etc/redis/redis.conf` ýerleşýär. Faýly redaktirlemek üçin:

```bash
sudo nano /etc/redis/redis.conf
```

### 4.1 Esasy Sazlamalar

* **Port**: Adaty ýagdaýdaNation: Adaty port 6379.
  ```conf
  port 6379
  ```
* **Yzygiderli Saklama**: Maglumatlary diskde saklamak üçin:
  ```conf
  appendonly yes
  ```
* **Maksimum ýadyş**: Redis-iň ulanýan ýadyşyny çäklendirmek:
  ```conf
  maxmemory 100mb
  ```

Sazlamalary ýerine ýetirmek üçin:

```bash
sudo systemctl restart redis
```

## 5. Mysal: Ýönekeý Keşirleme Ulanylyşy

Redis-i ýerli ulgamda keşirleme üçin ulanyp bilersiňiz. Aşakda ýönekeý bir mysal:

1. **Redis CLI-ni açyň**:

   ```bash
   redis-cli
   ```
2. **Keş maglumatyny goşmak**:

   ```bash
   SET page:home "<html><body>Salam, Redis!</body></html>" EX 60
   ```

   * `EX 60`: Maglumat 60 sekunt saklanar (TTL).
3. **Maglumat almak**:

   ```bash
   GET page:home
   ```
4. **Maglumatlaryň ýoklugyny barlamak**:
   60 sekuntdan soň `GET page:home` buýrugy `nil` gaýtarar.
5. **Redis-iň ýagdaýyny barlamak**:

   ```bash
   INFO MEMORY
   ```

## 6. Ýerli Ulgamda Redis Ulanylyşynyň Üstünlikleri

* **Çaltlyk**: Ýatda saklama sebäpli soraglar gaty çalt ýerine ýetirilýär.
* **Ýönekeýlik**: Anahtar-deger strukturasy ulanmagy aňsatlaşdyrýar.
* **Synag Amatlylygy**: Ýerli ulgamda keş ýa-da seans dolandyryşy üçin ideal.

## 7. Howpsuzlyk we Maslahatlar

* **Parol Goýmak**: Ýerli ulgamda parol gerek bolmasa-da, real proýektlerde parol goşuň:
  ```conf
  requirepass your_password
  ```
* **Ýazgylary Barlamak**: Redis-iň log faýllaryny gözden geçiriň:
  ```bash
  sudo tail -f /var/log/redis/redis-server.log
  ```
* **Maglumatlaryň Ýitmezligi**: Yzygiderli saklama üçin `appendonly yes` ulanyň.
* **Resurs Dolandyrylyşy**: Ýerli ulgamda ýadyş ulanyşyny çäklendirmek üçin `maxmemory` sazlaň.

## 8. Netije

Redis, çalt, ýeňil we dürli maglumat strukturalaryny goldaýan güýçli bir sistemdir. Bu resminama, Redis-i ýerli ulgamda gurnamagy, esas buýruklary we ýönekeý bir keşirleme mysalyny görkezdi. Goşmaça maglumat üçin Redis-iň resmi resminamalaryna göz aýlap bilersiňiz.
