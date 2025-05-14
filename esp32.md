# ESP32 bilen MQTT Protokolyny Ulanylyşy

Bu resminama, ESP32 mikrokontrollery bilen MQTT protokolyny ulanyp, habar ýaýratmak (publish) we habar almak (subscribe) işleriniň nädip ýerine ýetirilýändigini jikme-jik düşündirýär. Mysal üçin, Mosquitto MQTT brokeri bilen ESP32-niň üns berişini görkezeris.

## 1. Giriş

ESP32, IoT (Internet of Things) taslamalary üçin giňden ulanylýan, Wi-Fi we Bluetooth goldawly mikrokontrollerdir. MQTT protokoly, ygtybarly we ýeňil habarlaşyk üpjün edýär, bu bolsa ESP32-ni IoT enjamlary bilen işleşmek üçin ideal edýär.

Bu resminamada:

* ESP32-ni MQTT brokerine birikdirmek,
* Habar ýaýratmak we almak üçin Arduino IDE ulanmak,
* Howpsuzlyk çäreleri barada gysga maglumat berleris.

## 2. Zerurlyklar

Başlamazdan öň aşakdaky enjamlar we programmalar taýyn bolmaly:

* **Donanma**:
  * ESP32 (DevKit ýa-da başga model)
  * USB kabeli (programmirlemek üçin)
  * Kompýuter
  * Wi-Fi şebekesi
* **Programma**:
  * Arduino IDE (soňky wersiýa)
  * ESP32 Arduino Core (Arduino IDE-de gurnalmaly)
  * PubSubClient kitaphanasy (MQTT üçin)
  * Mosquitto MQTT brokeri (ýerli ýa-da uzaak serwerde işleýän)
* **Maglumatlar**:
  * Wi-Fi şebekesiniň SSID we paroly
  * MQTT brokeriniň IP adresi ýa-da domen ady
  * MQTT broker porty (adaty ýagdaýda 1883 ýa-da TLS üçin 8883)

## 3. Mosquitto MQTT Brokeriniň Taýýarlanmagy

Mosquitto MQTT brokerini kompýuteriňizde ýa-da uzaak serwerde gurnamaly. Gurnamak üçin şu ädimleri ýerine ýetiriň:

1. **Mosquitto-nyň Ýüklenmegi** (Ubuntu üçin):
   ```bash
   sudo apt update
   sudo apt install mosquitto mosquitto-clients
   ```
2. **Brokeriň Başladylmagy**:
   ```bash
   sudo systemctl start mosquitto
   sudo systemctl enable mosquitto
   ```
3. **Brokeriň Sazlamalary**:
   Brokeriň ýerli kompýuterde işleýändigini ýa-da uzaak serwerde barlygyny barlaň. Adaty port: 1883. Eger TLS ulanýan bolsaňyz, 8883 portuny we zerur sertifikatlary sazlamaly.

## 4. ESP32-niň Taýýarlanmagy

### 4.1 Arduino IDE-ni Sazlamak

1. **Arduino IDE-ni Ýüklemek**: Arduino-nyň resmi sahypasyndan soňky wersiýany ýükläň we gurnamaly.
2. **ESP32 Goldawyny Goşmak**:
   * Arduino IDE-ni açyň.
   * `File > Preferences` bölümine giriň.
   * `Additional Boards Manager URLs` bölümine şu ýoly goşuň:
     ```
     https://raw.githubusercontent.com/espressif/arduino-esp32/master/package_esp32_index.json
     ```
   * `Tools > Board > Boards Manager` bölümine giriň, “ESP32” alyň we gurnamaly.
3. **PubSubClient Kitaphanasyny Gurnamak**:
   * `Sketch > Include Library > Manage Libraries` bölümine giriň.
   * “PubSubClient” kitaphanasyny gözläň we gurnamaly.

### 4.2 ESP32-ni Programmirlemek

Aşakdaky kod, ESP32-niň Wi-Fi şebekesine birigip, MQTT brokerine baglanmagyny we habar ýaýratmagyny/almagyny üpjün edýär.

#### Kod Mysaly

```cpp
#include <WiFi.h>
#include <PubSubClient.h>

// Wi-Fi we MQTT sazlamalary
const char* ssid = "SIZIN_WIFI_ADYNYZ";
const char* password = "SIZIN_WIFI_PAROLYNYZ";
const char* mqtt_server = "BROKER_IP_ADRESI"; // Mysal: "192.168.1.100"
const int mqtt_port = 1883;
const char* mqtt_user = "ULANYJY_ADY"; // Eger ulanmaýan bolsa, boş goýuň
const char* mqtt_password = "PAROL"; // Eger ulanmaýan bolsa, boş goýuň
const char* mqtt_client_id = "ESP32_Client";

// Mowzuklar
const char* topic_subscribe = "home/test/subscribe";
const char* topic_publish = "home/test/publish";

WiFiClient espClient;
PubSubClient client(espClient);
unsigned long lastMsg = 0;

void setup_wifi() {
  delay(10);
  Serial.println("WiFi-a birigýär...");
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("");
  Serial.println("WiFi-a birikdi!");
  Serial.println(WiFi.localIP());
}

void callback(char* topic, byte* payload, unsigned int length) {
  Serial.print("Habar geldi [");
  Serial.print(topic);
  Serial.print("]: ");
  for (int i = 0; i < length; i++) {
    Serial.print((char)payload[i]);
  }
  Serial.println();
}

void reconnect() {
  while (!client.connected()) {
    Serial.print("MQTT-a birigýär...");
    if (client.connect(mqtt_client_id, mqtt_user, mqtt_password)) {
      Serial.println("birikdi!");
      client.subscribe(topic_subscribe);
    } else {
      Serial.print("şowsuz, rc=");
      Serial.print(client.state());
      Serial.println(" 5 sekunt garaşýar...");
      delay(5000);
    }
  }
}

void setup() {
  Serial.begin(115200);
  setup_wifi();
  client.setServer(mqtt_server, mqtt_port);
  client.setCallback(callback);
}

void loop() {
  if (!client.connected()) {
    reconnect();
  }
  client.loop();

  unsigned long now = millis();
  if (now - lastMsg > 5000) {
    lastMsg = now;
    String msg = "Salam, ESP32!";
    client.publish(topic_publish, msg.c_str());
    Serial.println("Habar ýaýradyldy: " + msg);
  }
}
```

#### Kod Barada Düşündiriş

* **Wi-Fi Baglanyşygy**: `setup_wifi()` funksiýasy ESP32-ni Wi-Fi şebekesine birikdirýär.
* **MQTT Baglanyşygy**: `PubSubClient` kitaphanasy bilen MQTT brokerine baglanýar.
* **Habar Almak**: `callback()` funksiýasy, abuna bolnan mowzukdan gelen habarlary kabul edýär.
* **Habar Ýaýratmak**: Her 5 sekuntda `home/test/publish` mowzugyna “Salam, ESP32!” habary ýaýradylýar.
* **Sazlamalar**: Kodda Wi-Fi we MQTT maglumatlaryny özüňize laýyklykda üýtgetmeli.

## 5. Programmany ESP32-e Ýüklemek

1. ESP32-ni kompýutere USB kabeli bilen birikdiriň.
2. Arduino IDE-de `Tools > Board` bölüminden “ESP32 Dev Module” saýlaň.
3. `Tools > Port` bölüminden dogry porty saýlaň.
4. Kody ýükläň (`Upload` düwmesine basyň).
5. Serial Monitor-y açyň (`Tools > Serial Monitor`, 115200 baud) we netijeleri göruň.

## 6. Synag

1. **Mosquitto bilen Synag**:
   * Kompýuteriňizde Mosquitto brokeri işlese, aşakdaky buýruk bilen mowzugy yzarlaň:

     ```bash
     mosquitto_sub -h localhost -t "home/test/publish"
     ```
   * ESP32-niň ýaýradýan “Salam, ESP32!” habaryny görüň.
   * Başga terminalda habar ýaýratmak üçin:

     ```bash
     mosquitto_pub -h localhost -t "home/test/subscribe" -m "Test habary"
     ```

     Bu habar ESP32-niň Serial Monitor-unda görner.
2. **Netijeleri Barlamak**: ESP32-niň Wi-Fi we MQTT baglanyşyglarynyň dogry işleýändigini Serial Monitor-dan görüp bilersiňiz.

## 7. Howpsuzlyk Çäreleri

* **TLS/SSL Ulanyň**: Eger MQTT brokeriňiz uzaak serwerde ýerleşýän bolsa, TLS/SSL bilen şifrirlenen baglanyşyk ulanyň. Bu üçin `PubSubClient` kitaphanasyny TLS goldawy bilen sazlamaly we zerur sertifikatlary goşmaly.
* **Ulanyjy we Parol**: MQTT brokerinde ulanyjy ady we parol sazlamalaryny işjeňleşdiriň:

  ```bash
  sudo mosquitto_passwd -c /etc/mosquitto/passwd ulanyjy_ady
  ```

  Sazlama faýlyna şu setiri goşuň:

  ```bash
  password_file /etc/mosquitto/passwd
  ```
* **Giriş Gözegçiligi**: Brokerde ACL (Access Control List) ulanyp, diňe ygtyýarly mowzuklara giriş bermeli.

## 8. Netije

Bu resminama, ESP32 bilen MQTT protokolyny ulanmagyň esasy ädimlerini öz içine alýar. Kod mysaly, ESP32-niň MQTT brokerine birigip habar ýaýratmagy we almagy üçin ýeterlik başlangyç nokady hödürleýär. IoT taslamalaryňyzda bu mysaly öz islegleriňize laýyklykda üýtgedip ulanyp bilersiňiz.

Goşmaça soraglaryňyz bar bolsa, Mosquitto-nyň ýa-da ESP32-niň resmi resminamalaryna göz aýlap bilersiňiz.
