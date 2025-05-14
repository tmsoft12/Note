# Docker Barada: Giriş we Ýerli Ulanylyş

Bu resminama, Docker platformasynyň näme bolandygyny, ýerli ulgamda nädip gurnalmaly we nädip ulanylmalydygyny gysgaça düşündirýär. Docker bilen ýönekeý bir mysal hem berilýär.

## 1. Docker Näme?

Docker, programmalar we olaryň baglylyklaryny (kütüphanalar, sazlamalar) “konteýner” diýilýän yzygiderli we ýeňil gurşawlarda işledýän açyk platformadyr. Konteýnerler, programmanyň dürli ulgamlarda birmeňzeş işlemesini üpjün edýär.

### 1.1 Esasy Käsirler

* **Konteýner**: Programma we baglylyklaryny öz içine alýan ýeňil gurşaw.
* **Obraz (Image)**: Konteýneri döretmek üçin ulanylýan şablon (mysal: `nginx` obrazy).
* **Dockerfile**: Öz obrazyňyzy döretmek üçin sazlama faýly.
* **Docker Hub**: Obrazlary ýüklemek we paýlaşmak üçin onlaýn ammar.

## 2. Docker-i Ýerli Ulgamda Gurnamak (Ubuntu)

Docker-i Ubuntu-da gurnamak üçin şu ädimleri ýerine ýetiriň:

1. **Ulgamy täzelemek**:

   ```bash
   sudo apt update
   sudo apt upgrade
   ```
2. **Docker-iň resmi ammarlaryny goşmak**:

   ```bash
   sudo apt install ca-certificates curl gnupg lsb-release
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
   echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```
3. **Docker-i gurnamak**:

   ```bash
   sudo apt update
   sudo apt install docker-ce docker-ce-cli containerd.io
   ```
4. **Docker-iň işleýändigini barlamak**:

   ```bash
   docker --version
   sudo systemctl status docker
   ```
5. **Ulanyjy rugsady** (root bolmazdan ulanmak üçin):

   ```bash
   sudo usermod -aG docker $USER
   ```

   Soňra terminaly täzeden açyň ýa-da `newgrp docker` buýrugyny ulanyň.

## 3. Esasy Docker Buýruklary

Docker-i ulanmak üçin käbir esasy buýruklar:

* **Obrazlary görmek**: `docker images`
* **Konteýnerleri görmek**: `docker ps -a`
* **Obraz ýüklemek**: `docker pull <obraz_ady>` (mysal: `docker pull nginx`)
* **Konteýner başlatmak**: `docker run -d --name <konteýner_ady> -p <host_port>:<konteýner_port> <obraz_ady>`
* **Konteýneri duruzmak**: `docker stop <konteýner_ady>`
* **Konteýneri öçürmek**: `docker rm <konteýner_ady>`
* **Obrazy ýok etmek**: `docker rmi <obraz_ady>`

## 4. Mysal: Nginx Konteýneri Işletmek

Docker bilen ýerli ulgamda ýönekeý bir Nginx web serwerini işledip bilersiňiz.

1. **Nginx obrazy ýüklemek**:

   ```bash
   docker pull nginx
   ```
2. **Nginx konteýnerini başlatmak**:

   ```bash
   docker run -d --name nginx-container -p 8080:80 nginx
   ```

   * `-d`: Konteýneriň fonda işlemesini üpjün edýär.
   * `-p 8080:80`: Ýerli 8080 portuny konteýneriň 80 portuna baglaýar.
3. **Synag etmek**:
   Brauzeriňizde `http://localhost:8080` adresine giriň. Nginx-iň hoş geldiň sahypasyny görersiňiz. Ýa-da:

   ```bash
   curl http://localhost:8080
   ```
4. **Konteýneri duruzmak we öçürmek**:

   ```bash
   docker stop nginx-container
   docker rm nginx-container
   ```

## 5. Ýerli Ulgamda Docker-iň Üstünlikleri

* **Yzygyderlilik**: Konteýnerler, programmanyň ýerli we başga gurşawlarda birmeňzeş işlemesini üpjün edýär.
* **Ýeňillik**: Konteýnerler wirtual maşynlardan has az ýer tutýar we çalt işleýär.
* **Synag Amatlylygy**: Ýerli ulgamda dürli programmalar we wersiýalar çalt synag edilip bilner.

## 6. Maslahatlar

* **Howpsuzlyk**: Docker Hub-dan diňe resmi ýa-da ygtybarly obrazlary ulanyň.
* **Ýazgylary Barlamak**: Konteýneriň ýazgylaryny görmek üçin `docker logs <konteýner_ady>` buýrugyny ulanyň.
* **Resurs Çäklendirmeleri**: Ýerli ulgamda resurslary sarp etmezlik üçin `--cpus` ýa-da `--memory` flaglaryny ulanyň.
* **Docker Compose**: Birnäçe konteýneri dolandyrmak üçin `docker-compose` ulanyp bilersiňiz.

## 7. Netije

Docker, programmalar we gurşawlary yzygiderli we ýeňil ýagdaýda işletmek üçin güýçli guraldyr. Bu resminama, Docker-i ýerli ulgamda gurnamagyň we ulanmagyň esasy ädimlerini görkezdi. Goşmaça maglumat üçin Docker-iň resmi resminamalaryna ýa-da Docker Hub-a göz aýlap bilersiňiz.
