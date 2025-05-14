# Linux Barada: Giriş, Ubuntu Gurnamak we Esasy Buýruklar

Bu resminama, Linux işlediş ulgamynyň näme bolandygyny, Ubuntu distribuýasiýasyny ýerli ulgamda nädip gurnalmalydygyny we köp ulanylýan esasy buýruklary gysgaça düşündirýär.

## 1. Linux Näme?

Linux, açyk kodly, ygtybarly we özbaşdak sazlanyp bolýan işlediş ulgamydyr. 1991-nji ýylda Linus Torvalds tarapyndan döredilen Linux, häzirki wagtda serwerlerden IoT enjamlaryna çenli giň ulanylýar. Ubuntu, Linux-yň iň meşhur distribuýasiýalaryndan biridir we ulanmaga aňsatlygy bilen tanalýar.

### 1.1 Esasy Aýratynlyklar

* **Açyk Kod**: Kodlary erkin ulanyp, üýtgedip we paýlaşyp bolýar.
* **Ygtybarlylyk**: Wiruslara we zarrarly programmalara garşy güýçli.
* **Sazlanyp Bolýanlyk**: Ulanyjylar ulgamy öz isleglerine görä sazlap bilýär.
* **Köp Ulanylyş**: Serwerler, şahsy kompýuterler we IoT enjamlary üçin ulanylýar.

## 2. Ubuntu-ny Ýerli Ulgamda Gurnamak

Ubuntu-ny ýerli kompýutere gurnamak üçin şu ädimleri ýerine ýetiriň:

1. **Ubuntu ISO Obrazyny Ýüklemek**:
   * Ubuntu-nyň resmi sahypasyndan ([https://ubuntu.com/download](https://ubuntu.com/download)) soňky wersiýany ýükläň.
   * Mysal: Ubuntu 22.04 LTS.
2. **Boot Edilýän USB Döretmek**:
   * Rufus (Windows) ýa-da `dd` buýrugy (Linux/macOS) ýaly gurallar bilen ISO faýlyny USB-e ýazyň.
   * Mysal (`dd` bilen):

     ```bash
     sudo dd if=ubuntu-22.04.iso of=/dev/sdX bs=4M status=progress && sync
     ```

     **Not**: `/dev/sdX` ýerine USB-niň dogry adyny ýazyň.
3. **BIOS/UEFI Sazlamalary**:
   * Kompýuteri ýapyp açyň we BIOS/UEFI menýusyna giriň (adaty ýagdaýda F2, Del ýa-da Esc düwmesi).
   * Boot tertibinde USB-ni ilkinji ýere goýuň.
4. **Ubuntu Gurnamak**:
   * USB-den boot ediň, Ubuntu-nyň gurnama ekranyna giriň.
   * Dil, klawiatura we disk bölünişigi sazlamalaryny saýlaň.
   * “Normal gurnama” ýa-da “Minimal gurnama” saýlap, gurnamagy tamamlaň.
5. **Gurnamadan Soň Barlag**:
   * Kompýuteri täzeden açyň we Ubuntu-nyň dogry işleýändigini barlaň.

## 3. Esasy Ubuntu Buýruklary

Ubuntu-da terminal arkaly işlemek üçin köp ulanylýan buýruklar:

### 3.1 Faýl we Katalog Dolandyryşy

* **Häzirki katalogy görmek**: `pwd`
* **Faýllary we kataloglary görmek**: `ls` ýa-da `ls -la` (gizlin faýllar bilen)
* **Katalog üýtgetmek**: `cd <katalog_ady>` (mysal: `cd Documents`)
* **Täze katalog döretmek**: `mkdir <katalog_ady>`
* **Täze faýl döretmek**: `touch <faýl_ady>`
* **Faýly ýa-da katalogy öçürmek**: `rm <faýl_ady>` ýa-da `rm -r <katalog_ady>`
* **Faýly göçürmek**: `cp <faýl_ady> <täze_ýer>`
* **Faýly ýa-da katalogy göçürmek**: `mv <faýl_ady> <täze_ýer>`

### 3.2 Ulgam Dolandyryşy

* **Ulgamy täzelemek**: `sudo apt update && sudo apt upgrade`
* **Programma gurnamak**: `sudo apt install <program_ady>` (mysal: `sudo apt install vim`)
* **Programmany aýyrmak**: `sudo apt remove <program_ady>`
* **Işleýän prosesleri görmek**: `top` ýa-da `htop`
* **Ulgam ýagdaýyny barlamak**: `df -h` (disk ýeri) ýa-da `free -m` (ýadyş)

### 3.3 Faýl Içini Barlamak we Redaktirlemek

* **Faýlyň mazmunyny görmek**: `cat <faýl_ady>`
* **Faýly setir-setir görmek**: `less <faýl_ady>` ýa-da `more <faýl_ady>`
* **Faýly redaktirlemek**: `nano <faýl_ady>` ýa-da `vim <faýl_ady>`
* **Faýlda gözleg**: `grep <söz> <faýl_ady>`

### 3.4 Şebeke Buýruklary

* **IP adresini görmek**: `ip addr` ýa-da `ifconfig`
* **Şebeke baglanyşygyny barlamak**: `ping google.com`
* **Faýl ýüklemek**: `wget <url>` ýa-da `curl -O <url>`

### 3.5 Ulanyjy we Rugsat Buýruklary

* **Ulanyjyny üýtgetmek**: `su <ulanyjy_ady>` ýa-da `sudo -u <ulanyjy_ady>`
* **Täze ulanyjy döretmek**: `sudo adduser <ulanyjy_ady>`
* **Faýl rugsatlaryny üýtgetmek**: `chmod <rugsat> <faýl_ady>` (mysal: `chmod 755 script.sh`)
* **Faýl eýesini üýtgetmek**: `chown <ulanyjy> <faýl_ady>`

## 4. Ubuntu Sazlamalary

Ubuntu-nyň esasy sazlama faýllary adaty ýagdaýda `/etc/` katalogynda ýerleşýär. Möhüm faýllar:

* **`/etc/apt/sources.list`**: Programma ammarlaryny sazlaýar.
* **`/etc/fstab`**: Disk bölünişiklerini dolandyrýar.
* **`/etc/environment`**: Umumy ulgam üýtgeýjilerini sazlaýar.

### 4.1 Mysal: Programma Ammarlaryny Täzelemek

Ammar faýlyny redaktirlemek üçin:

```bash
sudo nano /etc/apt/sources.list
```

Täze ammar goşuň (mysal: Ubuntu-nyň resmi ammarlary) we täzeleň:

```bash
sudo apt update
```

## 5. Mysal: Ýönekeý Faýl Dolandyryşy

Ubuntu terminalynda ýönekeý bir faýl dolandyryş mysaly:

1. **Täze katalog döretmek**:
   ```bash
   mkdir my_project
   cd my_project
   ```
2. **Faýl döretmek we mazmun goşmak**:
   ```bash
   echo "Salam, Ubuntu!" > test.txt
   ```
3. **Faýly görmek**:
   ```bash
   cat test.txt
   ```
4. **Faýly göçürmek**:
   ```bash
   cp test.txt test_backup.txt
   ```
5. **Faýly öçürmek**:
   ```bash
   rm test.txt
   ```

## 6. Ýerli Ulgamda Ubuntu Ulanylyşynyň Üstünlikleri

* **Erkinlik**: Açyk kodlylygy sebäpli programma we sazlamalar mugt.
* **Ygtybarlylyk**: Wiruslara we zarrarly programmalara garşy güýçli.
* **Synag Amatlylygy**: IoT, serwer ýa-da programma döretmek üçin ideal.
* **Resurs Ünemligi**: Köp ýeňil distribuýasiýalar az ýadyş sarp edýär.

## 7. Maslahatlar

* **Buýruklary Öwrenmek**: `man <buýruk>` bilen buýruk barada maglumat alyň (mysal: `man ls`).
* **Ýazgylary Barlamak**: Hatalary ýüze çykarmak üçin log faýllaryny gözden geçiriň:
  ```bash
  sudo tail -f /var/log/syslog
  ```
* **Rugsatlar**: Faýl we katalog rugsatlaryny üns bilen sazlaň (`chmod`, `chown`).
* **Terminal Ýönekeýleşdirme**: `.bashrc` faýlyna ýörite buýruklar goşup terminaly sazlap bilersiňiz:
  ```bash
  nano ~/. LADbashrc
  ```

## 8. Netije

Linux, ygtybarly, sazlanyp bolýan we erkin işlediş ulgamydyr. Ubuntu, Linux-yň ulanmaga aňsat distribuýasiýasy hökmünde ýerli ulgamda programma döretmek, synag etmek we dolandyryş üçin ideal. Bu resminama, Ubuntu-ny gurnamagy we esasy buýruklary görkezdi. Goşmaça maglumat üçin Ubuntu-nyň resmi resminamalaryna göz aýlap bilersiňiz.
