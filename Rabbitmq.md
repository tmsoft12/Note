# RabbitMQ Barada: Giriş, Gurnamak we Ulanylyş

Bu resminama, RabbitMQ habarlaşyk sisteminiň näme bolandygyny, esas aýratynlyklaryny, ýerli ulgamda (Ubuntu) nädip gurnalmalydygyny, esas buýruklary we ýönekeý bir ulanylyş mysalyny gysgaça düşündirýär.

## 1. RabbitMQ Näme?

RabbitMQ, açyk kodly, ygtybarly we giňeldip bolýan habarlaşyk (message queue) sistemidir. AMQP (Advanced Message Queuing Protocol) protokolyny esas tutýar we programmalar arasynda habar alyş-çalşygy dolandyrmak üçin ulanylýar. Mikroservisler, IoT we uly ulgamlar üçin ideal.

### 1.1 Esasy Aýratynlyklar

* **Habar Nobatlary**: Habarlary yzygiderli we ygtybarly ýagdaýda nobatda saklaýar.
* **Ýeňillik**: Az resurs sarp edýär we çalt işleýär.
* **Giňeldip Bolýanlyk**: Exchange tipleri (direct, topic, fanout) we pluginler bilen özleşdirilip bolýar.
* **Kross-Platforma**: Go, Python, Java ýaly köp dillerde ulanylyp bolýar.
* **Ygtybarlylyk**: Habar ýitmesin diye ACK (acknowledgment) we yzygiderli saklama goldaýar.

### 1.2 Ulanylyş Ýagdaýlary

* Mikroservisler arasynda habarlaşyk.
* Ulanyjy tapgyr dolandyryşy (task queues).
* IoT enjamlaryndan maglumat ýygnamak.
* Log we monitoring sistemleri.

## 2. RabbitMQ-ny Ýerli Ulgamda Gurnamak (Ubuntu)

RabbitMQ-ny Ubuntu-da gurnamak üçin şu ädimleri ýerine ýetiriň:

1. **Ulgamy täzelemek**:

   ```bash
   sudo apt update
   sudo apt upgrade
   ```
2. **Erlang ýüklemek** (RabbitMQ Erlang-da ýazylandyr):

   ```bash
   sudo apt install erlang
   ```
3. **RabbitMQ-ny gurnamak**:

   * RabbitMQ ammarlaryny goşmak:
     ```bash
     curl -fsSL https://github.com/rabbitmq/signing-keys/releases/download/3.0/rabbitmq-release-signing-key.asc | sudo gpg --dearmor -o /usr/share/keyrings/rabbitmq-archive-keyring.gpg
     echo "deb [signed-by=/usr/share/keyrings/rabbitmq-archive-keyring.gpg] http://ppa.launchpad.net/rabbitmq/rabbitmq-erlang/ubuntu $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/rabbitmq.list
     ```
   * RabbitMQ-ny ýüklemek:
     ```bash
     sudo apt update
     sudo apt install rabbitmq-server
     ```
4. **RabbitMQ-nyň işleýändigini barlamak**:

   ```bash
   sudo systemctl status rabbitmq-server
   ```

   Adaty ýagdaýda `localhost:5672` portynda işleýär.
5. **RabbitMQ-ny awtomatik başlatmak**:

   ```bash
   sudo systemctl enable rabbitmq-server
   ```
6. **Web dolandyryş panelini işjeňleşdirmek**:

   ```bash
   sudo rabbitmq-plugins enable rabbitmq_management
   ```

   Brauzerde `http://localhost:15672` adresine giriň. Adaty ulanyjy: `guest`, parol: `guest`.

## 3. Esasy RabbitMQ Buýruklary

RabbitMQ-ny dolandyrmak üçin şu buýruklar ulanylýar:

* **Serweri başlatmak**: `sudo systemctl start rabbitmq-server`
* **Serweri duruzmak**: `sudo systemctl stop rabbitmq-server`
* **Serweri täzeden başlatmak**: `sudo systemctl restart rabbitmq-server`
* **Ýagdaýy barlamak**: `sudo systemctl status rabbitmq-server`
* **Pluginleri görmek**: `sudo rabbitmq-plugins list`
* **Täze ulanyjy döretmek**:
  ```bash
  sudo rabbitmqctl add_user <ulanyjy_ady> <parol>
  sudo rabbitmqctl set_user_tags <ulanyjy_ady> administrator
  sudo rabbitmqctl set_permissions -p / <ulanyjy_ady> ".*" ".*" ".*"
  ```
* **Guest ulanyjysyny öçürmek**:
  ```bash
  sudo rabbitmqctl delete_user guest
  ```

## 4. Ýönekeý Ulanylyş Mysaly (Go bilen)

Ýerli ulgamda RabbitMQ bilen habar ýollamak we almak üçin Go programmasy ýazalyň.

### 4.1 Zerurlyklar

* Go (gurnalan, mysal: 1.22).
* RabbitMQ (ýerli ulgamda işleýär).
* Go üçin AMQP kütüphanesi:
  ```bash
  go get github.com/rabbitmq/amqp091-go
  ```

### 4.2 Habar Ýollamak (`publish.go`)

```go
package main

import (
    "context"
    "log"
    "github.com/rabbitmq/amqp091-go"
)

func main() {
    conn, err := amqp091.Dial("amqp://guest:guest@localhost:5672/")
    if err != nil {
        log.Fatalf("Baglanyp bolmady: %v", err)
    }
    defer conn.Close()

    ch, err := conn.Channel()
    if err != nil {
        log.Fatalf("Kanal açylmady: %v", err)
    }
    defer ch.Close()

    q, err := ch.QueueDeclare(
        "hello", // nobat ady
        false,   // yzygiderli
        false,   // awto-öçürmek
        false,   // ýörite
        false,   // goşmaça argumentler
        nil,
    )
    if err != nil {
        log.Fatalf("Nobat döredilmedi: %v", err)
    }

    msg := "Salam, RabbitMQ!"
    err = ch.PublishWithContext(context.Background(),
        "",     // exchange
        q.Name, // nobat ady
        false,  // mandatory
        false,  // immediate
        amqp091.Publishing{
            ContentType: "text/plain",
            Body:        []byte(msg),
        })
    if err != nil {
        log.Fatalf("Habar ýollanmady: %v", err)
    }

    log.Println("Habar ýollady: ", msg)
}
```

### 4.3 Habar Almak (`consume.go`)

```go
package main

import (
    "log"
    "github.com/rabbitmq/amqp091-go"
)

func main() {
    conn, err := amqp091.Dial("amqp://guest:guest@localhost:5672/")
    if err != nil {
        log.Fatalf("Baglanyp bolmady: %v", err)
    }
    defer conn.Close()

    ch, err := conn.Channel()
    if err != nil {
        log.Fatalf("Kanal açylmady: %v", err)
    }
    defer ch.Close()

    q, err := ch.QueueDeclare(
        "hello",
        false,
        false,
        false,
        false,
        nil,
    )
    if err != nil {
        log.Fatalf("Nobat döredilmedi: %v", err)
    }

    msgs, err := ch.Consume(
        q.Name, // nobat
        "",     // consumer
        true,   // auto-ack
        false,  // ýörite
        false,  // no-local
        false,  // no-wait
        nil,
    )
    if err != nil {
        log.Fatalf("Consumer döredilmedi: %v", err)
    }

    forever := make(chan bool)
    go func() {
        for d := range msgs {
            log.Printf("Habar aldy: %s", d.Body)
        }
    }()

    log.Println(" [*] Habar garaşýar. Çykmak üçin CTRL+C")
    <-forever
}
```

### 4.4 Programmalary Işletmek

1. **RabbitMQ-nyň işleýändigini barlaň**:

   ```bash
   sudo systemctl status rabbitmq-server
   ```
2. **Habar ýollamak**:

   ```bash
   go run publish.go
   ```
3. **Habar almak**:
   Başga terminalda:

   ```bash
   go run consume.go
   ```

   Çykyş: `Habar aldy: Salam, RabbitMQ!`

## 5. Ýerli Ulgamda RabbitMQ Ulanylyşynyň Üstünlikleri

* **Aňsat Synag**: Ýerli ulgamda mikroservis ýa-da IoT habarlaşygy synag edilip bilýär.
* **Çalt Başlangyç**: Go, Python ýaly dillerde ýeňil integrasiýa.
* **Web Panel**: `http://localhost:15672` arkaly nobatlary we habarlary dolandyrmak aňsat.

## 6. Howpsuzlyk we Maslahatlar

* **Parol Howpsuzlygy**: Ýerli ulgamda `guest` ulanyjysy ulanylsa-da, real proýektlerde güýçli parol goýuň:
  ```bash
  sudo rabbitmqctl add_user myuser mypassword
  sudo rabbitmqctl delete_user guest
  ```
* **Ýazgylary Barlamak**: Hatalary ýüze çykarmak üçin:
  ```bash
  sudo tail -f /var/log/rabbitmq/rabbit@*.log
  ```
* **Baglanyşyk Çäklendirmeleri**: Ýerli ulgamyň daşyndan baglanyşyklar üçin firewall sazlaň.
* **Yzygiderli Saklama**: Möhüm habarlar üçin `durable` nobatlary ulanyň.

## 7. Netije

RabbitMQ, habarlaşyk sistemleri üçin ygtybarly we çalt çözgütdir. Ýerli ulgamda IoT, mikroservis ýa-da tapgyr dolandyryşy üçin synag etmek aňsat. Bu resminama, RabbitMQ-ny gurnamagy, esas buýruklary we Go bilen mysaly görkezdi. Goşmaça maglumat üçin RabbitMQ-nuň resmi resminamalaryna göz aýlap bilersiňiz.
