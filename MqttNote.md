# MQTT Protokoly: Gurnamak, Sazlamalar we Howpsuzlyk

Bu resminama, MQTT protokolynyň gurnamagy, sazlamalary, howpsuzlyk çäreleri we habar yzarlamagy barada jikme-jik düşündirilýär.

## 1. MQTT Gurnamak

### 1.1 MQTT Broker Gurnamak

MQTT broker, klientler arasynda habar alyş-çalyşyny üpjün edýän serwerdir. Iň köp ulanylýan broker programmalary **Mosquitto** we **EMQ X**dir. Bu resminamada, açyk çeşme we ulanmaga aňsat bolan **Mosquitto** brokerynyň gurnamagy barada aýdylýar.

#### Mosquitto Gurnamak

1. **Mosquitto-nyň Ýüklenmegi**
   Ubuntu operasion ulgamynda Mosquitto brokerini gurnamak üçin aşakdaky buýruklary işlediň:
   ```bash
   sudo apt update
   sudo apt install mosquitto mosquitto-clients
   ```
2. **Mosquitto-nyň Başladylmagy**
   Gurnamadan soň Mosquitto brokerini başlatmak üçin:
   ```bash
   sudo systemctl start mosquitto
   ```
3. **Awtomatik Başlatma Sazlamasy**
   Mosquitto-nyň ulgam her täzeden açylanda awtomatik işlemegi üçin:
   ```bash
   sudo systemctl enable mosquitto
   ```

### 1.2 MQTT Klient Gurnamak

MQTT klienti, brokere birigip habar ýaýratmak (publish) ýa-da abuna bolmak (subscribe) üçin ulanylýar. Mosquitto klient gurallary bu maksat üçin ulanylyp bilner:

```bash
sudo apt install mosquitto-clients
```

#### MQTT Klient Buýruklary

* **Habar Ýaýratmak (Publish)**:
  ```bash
  mosquitto_pub -h <broker_ip> -t "mowzuk_ady" -m "habar"
  ```
* **Habar Almak (Subscribe)**:
  ```bash
  mosquitto_sub -h <broker_ip> -t "mowzuk_ady"
  ```

## 2. MQTT Sazlamalary

### 2.1 Mosquitto Sazlamalary

Mosquitto-nyň sazlama faýly köplenç `/etc/mosquitto/mosquitto.conf` ýolunda ýerleşýär. Bu faýly redaktirlemek üçin:

```bash
sudo nano /etc/mosquitto/mosquitto.conf
```

#### Mysal Sazlama Üýtgeşmeleri

1. **Diňleýiş Portunyň Üýtgedilmegi**
   Mosquitto adaty ýagdaýda 1883 portuny ulanýar. Başga port ulanmak isleseňiz, sazlama faýlynda şu setiri goşuň ýa-da üýtgediň:
   ```bash
   listener 1883
   ```
2. **Howpsuz Baglanyşyk üçin TLS Sazlamalary**
   Şifrirlenen baglanyşyklar üçin TLS/SSL ulanyp bilersiňiz. Aşakdaky setirleri sazlama faýlyna goşuň:
   ```bash
   listener 8883
   cafile /etc/ssl/certs/ca-certificates.crt
   certfile /etc/ssl/certs/server.crt
   keyfile /etc/ssl/private/server.key
   ```

### 2.2 Mowzuk (Topic) Gurluşy

MQTT habarlary mowzuklar (topics) arkaly iberilýär. Mowzuklar iýerarhik gurluşda bolup biler. Mysal:

* `home/otag/temperatura`: Otagyň temperatura maglumaty
* `home/aşhana/çyglylyk`: Aşhananyň çyglylyk maglumaty

### 2.3 Hyzmat Hilli (QoS)

MQTT protokoly üç sany dürli Hyzmat Hilli (Quality of Service) derejesini hödürleýär:

* **QoS 0**: Habar diňe bir gezek iberilýär, gowşurylmagy barlanmaýar.
* **QoS 1**: Habar, alyjy kabul edýänçä gaýtadan iberilýär.
* **QoS 2**: Habar diňe bir gezek gowşurylýar we iki tarap hem gowşurylmagy tassyklaýar.

## 3. MQTT Howpsuzlygy

### 3.1 Şifrirlenen Baglanyşyklar (TLS/SSL)

Brokeriň howpsuzlygyny ýokarlandyrmak üçin TLS/SSL bilen şifrirlenen baglanyşyklar ulanylmalydyr.

1. **SSL Sertifikaty we Açarynyň Döredilmegi**
   OpenSSL bilen SSL sertifikaty döredip bilersiňiz:
   ```bash
   sudo openssl req -new -x509 -days 365 -keyout /etc/ssl/private/server.key -out /etc/ssl/certs/server.crt
   ```
2. **TLS/SSL Sazlamasy**
   Sazlama faýlyna şu setirleri goşuň:
   ```bash
   listener 8883
   cafile /etc/ssl/certs/ca-certificates.crt
   certfile /etc/ssl/certs/server.crt
   keyfile /etc/ssl/private/server.key
   ```

### 3.2 Ulanyjy Barlagy

Brokere diňe ygtyýarly ulanyjylaryň birigmegini üpjün etmek üçin ulanyjy barlagy ulanylyp bilner.

1. **Parol Faýlynyň Döredilmegi**
   Ulanyjy goşmak üçin:

   ```bash
   sudo mosquitto_passwd -c /etc/mosquitto/passwd ulanyjy_ady
   ```

   Soňra paroly giriň.
2. **Parol Faýlynyň Sazlamasy**
   Sazlama faýlyna şu setiri goşuň:

   ```bash
   password_file /etc/mosquitto/passwd
   ```

### 3.3 Howpsuzlyk Diwary we Giriş Gözegçiligi

Brokeriň howpsuzlygyny ýokarlandyrmak üçin diňe belli IP adresleriniň birigmegine rugsat berip bilersiňiz. Bu maksat üçin giriş gözegçilik sanawy (ACL) ulanylyp bilner:

1. Sazlama faýlyna şu setiri goşuň:
   ```bash
   acl_file /etc/mosquitto/acl
   ```
2. `/etc/mosquitto/acl` faýlynda rugsat berlen ulanyjylary we mowzuklary kesgit ivelation:
   ```bash
   user ulanyjy_ady
   topic readwrite home/#
   ```

## 4. MQTT Habarlarynyň Yzarlanmagy

### 4.1 Ýazgy (Log) Sazlamalary

Mosquitto-nyň ýazgy funksiýasyny işjeňleşdirmek üçin sazlama faýlyna şu setiri goşuň:

```bash
log_dest file /var/log/mosquitto/mosquitto.log
```

### 4.2 Ýazgy Derejeleri

Dürli ýazgy derejeleri sazlanyp bilner:

* `debug`: Jikme-jik maglumatlar
* `info`: Umumy maglumatlar
* `warning`: Duýduryşlar
* `error`: Ýalňyşlyklar

Mysal:

```bash
log_type debug
```

## 5. MQTT Saglyk Barlaglary

Brokeriň dogry işleýändigini barlamak üçin aşakdaky buýrugy ulanyň:

```bash
systemctl status mosquitto
```

Şeýle hem, habarlary yzarlamak üçin şu buýrugy ulanyp bilersiňiz:

```bash
mosquitto_sub -h localhost -t "mowzuk_ady"
```

## Netije

Bu resminama, MQTT brokeriniň gurnamagy, sazlamasy we howpsuzlygynyň üpjün edilmegi üçin zerur ädimleri öz içine alýar. Howpsuzlyk we öndürijilik üçin ähli ädimleri üns bilen ýerine ýetirmeli. Goşmaça soraglaryňyz üçin Mosquitto-nyň resmi resminamalaryna göz aýlap bilersiňiz.
