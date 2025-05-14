# Go (Golang) Barada: Giriş, Gurnamak we Ulanylyş

Bu resminama, Go (Golang) programmirleme diliniň näme bolandygyny, esas aýratynlyklaryny, ýerli ulgamda (Ubuntu) nädip gurnalmalydygyny we ýönekeý bir mysal programmasyny gysgaça düşündirýär.

## 1. Go Näme?

Go (ýa-da Golang), 2009-njy ýylda Google tarapyndan döredilen açyk kodly, ýönekeý we ýokary öndürijilikli programmirleme dilidir. Go, çaltlygy, ýönekeý sintaksisi we köp ulanyjyly (concurrency) goldawy bilen serwer programmalary, bulut esasly sistemler we IoT üçin giňden ulanylýar.

### 1.1 Esasy Aýratynlyklar

* **Ýönekeýlik**: Az ýörite açar sözler we aňsat öwrenilýän sintaksis.
* **Çaltlyk**: Kompilýasiýa we iş tizligi C/C++ ýaly dillere ýakyn.
* **Köp Ulanyjylyk**: Goroutines we kanallar (channels) bilen ýeňil concurrency goldawy.
* **Standart Kütüphana**: Şebeke, faýl dolandyryşy we test üçin giň kütüphana.
* **Kross-Platforma**: Windows, Linux, macOS we IoT enjamlary üçin goldaw.
* **Zerurlyklaryň Azlygy**: Statik binari faýllar döredýär, baglylyklar az.

### 1.2 Ulanylyş Ýagdaýlary

* Web serwerleri (mysal: REST API-ler).
* Bulut esasly programmalar (Kubernetes, Docker).
* Şebeke gurallary (mysal: proxy ýa-da monitoring).
* IoT we mikroservisler.

## 2. Go-ny Ýerli Ulgamda Gurnamak (Ubuntu)

Go-ny Ubuntu-da gurnamak üçin şu ädimleri ýerine ýetiriň:

1. **Ulgamy täzelemek**:

   ```bash
   sudo apt update
   sudo apt upgrade
   ```
2. **Go-ny ýüklemek**:

   * Go-nyň soňky wersiýasyny resmi sahypadan ýükläň (mysal: Go 1.22).
   * Terminalda:
     ```bash
     wget https://go.dev/dl/go1.22.5.linux-amd64.tar.gz
     sudo tar -C /usr/local -xzf go1.22.5.linux-amd64.tar.gz
     ```
3. **Go ýoluny sazlamak**:

   * `.bashrc` faýlyna Go ýoluny goşmak üçin:

     ```bash
     nano ~/.bashrc
     ```

     Aşakdaky setirleri goşuň:

     ```bash
     export PATH=$PATH:/usr/local/go/bin
     export GOPATH=$HOME/go
     ```
   * Sazlamalary ýerine ýetirmek üçin:

     ```bash
     source ~/.bashrc
     ```
4. **Go-nuň gurnalandygyny barlamak**:

   ```bash
   go version
   ```

   Çykyş: `go version go1.22.5 linux/amd64` ýaly bir zat görersiňiz.
5. **Go iş ýerini taýýarlamak**:

   ```bash
   mkdir -p $HOME/go/{bin,src,pkg}
   ```

## 3. Esasy Go Buýruklary

Go programmalaryny dolandyrmak üçin terminalda şu buýruklar ulanylýar:

* **Täze modul döretmek**: `go mod init <modul_ady>` (mysal: `go mod init example.com/myapp`)
* **Baglylyklary ýüklemek**: `go get <paket_ady>` (mysal: `go get github.com/gofiber/fiber/v2`)
* **Kody işlemek**: `go run <faýl_ady>.go`
* **Kody ýasamak**: `go build <faýl_ady>.go`
* **Test ýerine ýetirmek**: `go test`
* **Modul sazlamalaryny täzelemek**: `go mod tidy`

## 4. Ýönekeý Go Programmasy

Ýerli ulgamda ýönekeý bir Go programmasyny ýazyp we işledip göreris.

### 4.1 Mysal Kod (`main.go`)

```go
package main

import "fmt"

func main() {
    fmt.Println("Salam, Golang! Ýerli ulgamda ilkinji programmam.")
}
```

### 4.2 Programmany Işletmek

1. **Faýly döretmek**:

   ```bash
   mkdir myapp
   cd myapp
   nano main.go
   ```

   Ýokardaky kody ýazyň we ýatdyryň.
2. **Modul başlatmak**:

   ```bash
   go mod init myapp
   ```
3. **Programmany işlemek**:

   ```bash
   go run main.go
   ```

   Çykyş: `Salam, Golang! Ýerli ulgamda ilkinji programmam.`
4. **Programmany ýasamak**:

   ```bash
   go build main.go
   ./main
   ```

   Bu, işledilip bolýan faýl döredýär.

## 5. Ýerli Ulgamda Go Ulanylyşynyň Üstünlikleri

* **Çalt Başlangyç**: Ýönekeý gurnama we aňsat öwrenilýän sintaksis.
* **Ýerli Synag**: IoT, web ýa-da şebeke programmalaryny çalt synag etmek.
* **Baglylyksyzlyk**: Döredilen binari faýllar başga baglylyk talap etmeýär.
* **Kross-Kompilýasiýa**: Ýerli ulgamda başga platformalar üçin programma ýasap bolýar (mysal: `GOOS=windows go build`).

## 6. Maslahatlar

* **Standart Format**: Kody formatlamak üçin `go fmt <faýl_ady>.go` ulanyň.
* **Baglylyk Dolandyryşy**: Modullarda baglylyklary `go.mod` faýly bilen dolandyryň.
* **Hatalary Barlamak**: Loglary we hatalary dolandyrmak üçin `log` ýa-da `errors` paketini ulanyň.
* **Resminamalar**: Go-nuň resmi resminamalaryna (`go.dev/doc`) we pkg.go.dev sahypasyna göz aýlaň.
* **Goroutines**: Köp ulanyjylyk üçin `go func()` ýa-da kanallar ulanyň.

## 7. Netije

Go, ýönekeý, çalt we ýokary öndürijilikli programma döretmek üçin ideal dildir. Ýerli ulgamda web serwerleri, IoT ýa-da şebeke gurallary üçin aňsatlyk bilen ulanylyp bilýär. Bu resminama, Go-ny gurnamagy, esasy buýruklary we ýönekeý bir programmasyny görkezdi. Goşmaça maglumat üçin Go-nuň resmi resminamalaryna göz aýlap bilersiňiz.
