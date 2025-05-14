# Gammu Barada: Giriş, Gurnamak we Ulanylyş

Bu resminama, Gammu-nuň näme bolandygyny, esas aýratynlyklaryny, ýerli ulgamda (Ubuntu) nädip gurnalmalydygyny, esas buýruklary we ýönekeý bir SMS ýollamak mysalyny gysgaça düşündirýär.

## 1. Gammu Näme?

Gammu, açyk kodly, jübi telefonlary we GSM modemleri bilen işlemek üçin döredilen programma guralydyr. SMS ýollamak, almak, telefon kitaby dolandyryşy we başga mobil funksiýalary ýerine ýetirmek üçin ulanylýar. IoT proýektlerinde, SMS habarnamalary ýa-da uzaakdan dolandyryş üçin giňden ulanylýar.

### 1.1 Esasy Aýratynlyklar

* **SMS Dolandyryşy**: SMS ýollamak, almak we arhiwlemek.
* **Telefon Kitaby**: Kontaktlary dolandyrmak we maglumatlary göçürmek.
* **Modem Goldawy**: USB modemler, Bluetooth ýa-da seriýal portlar arkaly baglanyşyk.
* **Skriptleşdirme**: Skriptler arkaly awtomatiki tapgyrlar ýerine ýetirýär.
* **Kross-Platforma**: Linux, Windows we başga ulgamlarda işleýär.

### 1.2 Ulanylyş Ýagdaýlary

* IoT enjamlaryndan SMS habarnamalary ýollamak.
* Awtomatiki SMS esasly dolandyryş sistemleri.
* Telefon kitaby we maglumatlaryň ätiýaçlygyny saklamak.
* Uzakdan monitoring we habarlaşyk.

## 2. Gammu-ny Ýerli Ulgamda Gurnamak (Ubuntu)

Gammu-ny Ubuntu-da gurnamak üçin şu ädimleri ýerine ýetiriň:

1. **Ulgamy täzelemek**:

   ```bash
   sudo apt update
   sudo apt upgrade
   ```
2. **Gammu we zerur paketleri gurnamak**:

   ```bash
   sudo apt install gammu gammu-smsd
   ```
3. **USB modemiň ýa-da telefonuň baglanylandygyny barlamak**:

   * USB modem ýa-da telefony kompýutere birikdiriň.
   * Baglanyşygy barlamak üçin:

     ```bash
     lsusb
     ```

     Modem ýa-da telefon ýerleşýän porty görmek üçin:

     ```bash
     dmesg | grep tty
     ```

     Mysal çykyş: `/dev/ttyUSB0` ýa-da `/dev/ttyACM0`.
4. **Gammu sazlamalaryny taýýarlamak**:

   * Gammu üçin sazlama faýly döretmek:

     ```bash
     nano ~/.gammurc
     ```

     Aşakdaky mazmuny goşuň (porty öz modemiňize görä üýtgediň):

     ```ini
     [gammu]
     port = /dev/ttyUSB0
     connection = at
     ```
5. **Gammu-nyň işleýändigini barlamak**:

   ```bash
   gammu --identify
   ```

   Çykyşda modemiň ýa-da telefonyň maglumatlary (marka, model) görünmeli.
6. **Gammu SMS daemonyny işjeňleşdirmek** (SMS almak üçin):

   ```bash
   sudo systemctl enable gammu-smsd
   sudo systemctl start gammu-smsd
   ```

## 3. Esasy Gammu Buýruklary

Gammu-ny dolandyrmak üçin şu buýruklar ulanylýar:

* **Modemi barlamak**: `gammu --identify`
* **SMS ýollamak**:

  ```bash
  echo "Salam, Gammu!" | gammu --sendsms TEXT <telefon_nomeri>
  ```

  Mysal: `echo "Test" | gammu --sendsms TEXT +99312345678`
* **SMS almak**:

  ```bash
  gammu --getsms 0
  ```

  (0 – gelen gutusyndaky ähli SMS-ler).
* **Telefon kitabyndan kontakt almak**:

  ```bash
  gammu --getmemory ME
  ```
* **Bütin SMS-leri öçürmek**:

  ```bash
  gammu --deletesms 0
  ```
* **Gammu SMS daemonynyň ýagdaýyny barlamak**:

  ```bash
  sudo systemctl status gammu-smsd
  ```

## 4. Ýönekeý Ulanylyş Mysaly

Ýerli ulgamda Gammu bilen SMS ýollamak we almak üçin ýönekeý bir mysal.

### 4.1 SMS Ýollamak

1. **Modemiň baglanylandygyny barlaň**:

   ```bash
   gammu --identify
   ```
2. **SMS ýollamak**:

   ```bash
   echo "Salam, bu Gammu-dan test SMS!" | gammu --sendsms TEXT +99312345678
   ```

   **Not**: Telefon nomerini özüňiziňki ýa-da synag nomeri bilen çalyşyň.
3. **Ýollanan SMS-i barlamak**:

   * Gammu loglaryny barlamak üçin:
     ```bash
     sudo tail -f /var/log/gammu-smsd.log
     ```

### 4.2 SMS Almak

1. **Gammu SMS daemonynyň işleýändigini barlaň**:
   ```bash
   sudo systemctl status gammu-smsd
   ```
2. **Gelen SMS-leri görmek**:
   * Gammu adaty ýagdaýda gelen SMS-leri `/var/spool/gammu/inbox/` katalogynda saklaýar.
   * Faýllary görmek:
     ```bash
     ls /var/spool/gammu/inbox/
     ```
   * Mysal faýlyň mazmunyny görmek:
     ```bash
     cat /var/spool/gammu/inbox/<faýl_ady>
     ```

## 5. Ýerli Ulgamda Gammu Ulanylyşynyň Üstünlikleri

* **Aňsat Synag**: Ýerli ulgamda SMS esasly IoT ýa-da habarnama sistemlerini synag etmek aňsat.
* **Ýönekeýlik**: USB modem ýa-da köne telefon bilen çalt başlap bolýar.
* **Arzan Çözgüt**: IoT proýektleri üçin az çykdajyly habarlaşyk usuly.

## 6. Howpsuzlyk we Maslahatlar

* **Modem Baglanyşygy**: Modemiň dogry portda baglanylandygyny we ýerli ulgamyň USB portlarynyň işleýändigini barlaň.
* **Loglary Barlamak**: Hatalary ýüze çykarmak üçin Gammu loglaryny gözden geçiriň:
  ```bash
  sudo tail -f /var/log/gammu-smsd.log
  ```
* **SMS Ygtyýarlyklary**: Real proýektlerde SMS ýollamak üçin ygtyýarly nomerleri çäklendiriň.
* **Sazlama Faýly**: `.gammurc` faýlynyň diňe ulanyjy üçin okalýan bolmagyny üpjün ediň:
  ```bash
  chmod 600 ~/.gammurc
  ```

## 7. Netije

Gammu, SMS we mobil enjamlar bilen işlemek üçin ýönekeý we ygtybarly guraldyr. Ýerli ulgamda IoT proýektleri, habarnama sistemleri ýa-da synag üçin giň ulanylyp bilýär. Bu resminama, Gammu-ny gurnamagy, esas buýruklary we ýönekeý bir SMS mysalyny görkezdi. Goşmaça maglumat üçin Gammu-nuň resmi resminamalaryna göz aýlap bilersiňiz.
