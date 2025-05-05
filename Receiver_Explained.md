# Receiver
Kode didalam file ini adalah kode untuk menerima data dari node dan mengirimkannya ke broker MQTT. Kode ini juga menyimpan data yang diterima ke dalam file log di SD card. Berikut adalah penjelasan dari setiap bagian kode:

## 1. Libraries
```cpp
#include "ArduinoJson.h"
#include <vector>
#include "time.h"
#include "stdbool.h"
```

Library yang digunakan pada bagian ini adalah:

1. `"ArduinoJson.h"`:
    - Library ini digunakan untuk mengolah data dalam format JSON
    - Memudahkan proses parsing dan pembuatan objek JSON
    - Sangat berguna untuk komunikasi data terstruktur

2. `<vector>`:
    - Library C++ standar untuk array dinamis
    - Memungkinkan penyimpanan data dalam bentuk array yang ukurannya bisa berubah
    - Digunakan untuk menyimpan data sensor yang diterima

3. `"time.h"`:
    - Library untuk manajemen waktu
    - Menyediakan fungsi-fungsi terkait timestamp dan format waktu
    - Digunakan untuk mencatat waktu penerimaan data

4. `"stdbool.h"`:
    - Library standar C untuk tipe data boolean
    - Mendefinisikan tipe data `bool` dengan nilai `true` atau `false`
    - Digunakan untuk logika kondisional dalam program

Library-library ini bekerja sama untuk:
- Mengelola data sensor dalam format JSON
- Menyimpan data dalam array dinamis
- Mencatat waktu penerimaan data
- Mengatur logika program

## 2. Time Configuration
```cpp
char TimeNow[30];
const char *ntpServer = "pool.ntp.org";
const long gmtOffset_sec = 21600;
const int daylightOffset_sec = 3600;
```

Bagian ini mengonfigurasi pengaturan waktu dengan penjelasan sebagai berikut:

1. `char TimeNow[30]`:
    - Array karakter untuk menyimpan waktu saat ini
    - Ukuran 30 karakter cukup untuk format waktu "HH:MM:SS DD/MM/YYYY"
    - Digunakan untuk mencatat timestamp saat logging data

2. `const char *ntpServer = "pool.ntp.org"`:
    - Menentukan server NTP (Network Time Protocol)
    - pool.ntp.org adalah layanan NTP publik yang umum digunakan
    - Server ini digunakan untuk sinkronisasi waktu perangkat

3. `const long gmtOffset_sec = 21600`:
    - Offset zona waktu dalam detik (UTC+6)
    - 21600 detik = 6 jam
    - Menyesuaikan waktu server dengan zona waktu lokal

4. `const int daylightOffset_sec = 3600`:
    - Pengaturan Daylight Saving Time (DST) dalam detik
    - 3600 detik = 1 jam
    - Mengompensasi perubahan waktu saat DST aktif

Konfigurasi ini penting untuk:
- Mendapatkan waktu yang akurat dari internet
- Menyesuaikan dengan zona waktu lokal
- Mencatat waktu yang tepat saat logging data sensor

## 3. MQTT Configuration
```cpp
#include <PubSubClient.h>
#define mqtt_server "192.168.220.122"
#define mqtt_port 1883
#define mqtt_username ""
#define mqtt_password ""
```

Bagian ini mengonfigurasi pengaturan MQTT (Message Queuing Telemetry Transport) dengan penjelasan sebagai berikut:

1. `#include <PubSubClient.h>`:
    - Library untuk implementasi client MQTT
    - Memungkinkan perangkat untuk berkomunikasi dengan broker MQTT
    - Menyediakan fungsi-fungsi untuk publish dan subscribe

2. `#define mqtt_server "192.168.220.122"`:
    - Mendefinisikan alamat IP broker MQTT
    - Menggunakan IP lokal untuk koneksi ke broker
    - Format alamat IPv4 standar

3. `#define mqtt_port 1883`:
    - Menentukan port untuk koneksi MQTT
    - Port 1883 adalah port default untuk MQTT
    - Digunakan untuk komunikasi tanpa enkripsi

4. `#define mqtt_username ""`:
    - Kredensial username untuk autentikasi ke broker
    - Dikosongkan jika tidak memerlukan autentikasi
    - Bisa diisi sesuai konfigurasi broker

5. `#define mqtt_password ""`:
    - Kredensial password untuk autentikasi ke broker
    - Dikosongkan jika tidak memerlukan autentikasi
    - Bisa diisi sesuai konfigurasi broker

Konfigurasi ini penting untuk:
- Menentukan tujuan pengiriman data
- Mengatur parameter koneksi ke broker
- Menyediakan autentikasi jika diperlukan

## 4. MQTT Topics
```cpp
String client_id = "NODE-" + String(DEV_ID);
String TOPIC_IR = "PLN_NP_Testing_Iradian_";
String TOPIC_WS = "PLN_NP_Testing_WindSpeed_";
String TOPIC_WD = "PLN_NP_Testing_WindDirection_";
String TOPIC_TEMP = "PLN_NP_Testing_Temperature_";
String TOPIC_HUM = "PLN_NP_Testing_Humidity_";
String TOPIC_ATMP = "PLN_NP_Testing_AtmosphericPressure_";
String TOPIC_RAIN = "PLN_NP_Testing_RainFall_";
```

Bagian ini mendefinisikan topic-topic MQTT yang digunakan untuk pengiriman data dengan penjelasan sebagai berikut:

1. `String client_id`:
    - Identifikasi unik untuk client MQTT
    - Dibuat dengan menggabungkan "NODE-" dan ID perangkat
    - Memastikan setiap perangkat memiliki ID unik

2. Topic-topic sensor:
    - Format nama: "PLN_NP_Testing_[JenisSensor]_"
    - Akan ditambahkan ID node di akhir setiap topic
    - Digunakan untuk memisahkan data berdasarkan jenis sensor

Fungsi dari masing-masing topic:
- `TOPIC_IR`: Data iradiasi matahari
- `TOPIC_WS`: Data kecepatan angin
- `TOPIC_WD`: Data arah angin
- `TOPIC_TEMP`: Data temperatur
- `TOPIC_HUM`: Data kelembaban
- `TOPIC_ATMP`: Data tekanan atmosfer
- `TOPIC_RAIN`: Data curah hujan

## 5. WiFi and Ethernet Configuration
```cpp
WiFiClient wifiClient;

EthernetClient ethClient;
byte mac[] = { 0xDE, 0xED, 0xBA, 0xFE, 0xFE, 0xEF };

PubSubClient client;

String LoRaData = "";
```

Konfigurasi koneksi jaringan dengan penjelasan sebagai berikut:

1. `WiFiClient wifiClient`:
    - Objek untuk koneksi WiFi
    - Menangani komunikasi melalui WiFi
    - Digunakan saat mode WiFi aktif

2. `EthernetClient ethClient`:
    - Objek untuk koneksi Ethernet
    - Menangani komunikasi melalui kabel Ethernet
    - Digunakan saat mode Ethernet aktif

3. `byte mac[]`:
    - Alamat MAC untuk modul Ethernet
    - Format: 6 byte hexadecimal
    - Harus unik dalam jaringan

4. `PubSubClient client`:
    - Objek untuk komunikasi MQTT
    - Dapat menggunakan WiFi atau Ethernet
    - Menangani publish dan subscribe

5. `String LoRaData`:
    - String untuk menyimpan data yang diterima
    - Digunakan untuk data mentah dari LoRa
    - Akan diproses sebelum dikirim

## 6. Acknowledgment Message and Logging
```cpp
// set time delay to send ack message
#define DELAY_ACK 20000
// set interval to send data via MQTT Protocol
// #define INTERVAL_MQTT 300000
#define INTERVAL_MQTT 100000 //nyoba aja biar cepett
```

Pengaturan waktu untuk acknowledgment dan pengiriman data:

1. `DELAY_ACK 20000`:
    - Delay untuk mengirim pesan acknowledgment
    - Nilai dalam milidetik (20 detik)
    - Mencegah pengiriman ACK terlalu sering

2. `INTERVAL_MQTT 100000`:
    - Interval pengiriman data ke broker MQTT
    - Nilai dalam milidetik (100 detik)
    - Komentar menunjukkan nilai asli 300000 (5 menit)

## 7. Data Structure
```cpp
// to store received data
struct SensorData
{
    int id;
    float wspeed;
    float wdirect;
    float temp;
    float hum;
    float rain;
    int iradian;
    int atmp;
};
```

Struktur data untuk menyimpan data sensor dengan penjelasan setiap field:

1. `int id`:
    - ID unik untuk setiap node sensor
    - Tipe data integer
    - Mengidentifikasi sumber data

2. Data sensor (dalam satuan yang sesuai):
    - `float wspeed`: Kecepatan angin
    - `float wdirect`: Arah angin
    - `float temp`: Temperatur
    - `float hum`: Kelembaban
    - `float rain`: Curah hujan
    - `int iradian`: Iradiasi matahari
    - `int atmp`: Tekanan atmosfer

## 8. Vector to store sensor data
```cpp
std::vector<SensorData> node_data;
String ack_message = "";
```

Penyimpanan data dan pesan acknowledgment:

1. `std::vector<SensorData> node_data`:
    - Vector untuk menyimpan data sensor
    - Menggunakan struktur SensorData
    - Ukuran dinamis sesuai kebutuhan

2. `String ack_message`:
    - Menyimpan pesan acknowledgment
    - Format string kosong
    - Diisi saat perlu mengirim ACK

## 9. Reconnect Function
```cpp
void reconnect()
{
    int bcount = 0;
    while (!client.connected())
    {
        bcount++;
        customSerial.print("Attempting MQTT connection...\n");
        if (bcount > 10)
        {
            customSerial.println("Restart ESP");
            delay(500);
            ESP.restart();
        }
        if (client.connect(client_id.c_str(), mqtt_username, mqtt_password))
        {
            bcount = 0;
            customSerial.println("connected");
        }
        else
        {
            if (mode == 1)
            {
                WiFi.begin(ssid, password);
                customSerial.print("failed, rc=");
                customSerial.print(client.state());

                if (WiFi.status() != WL_CONNECTED)
                {
                    customSerial.print("WIFI CONNECTING");
                    int count = 0;
                    while (WiFi.status() != WL_CONNECTED)
                    {
                        delay(500);
                        count++;
                        customSerial.print(".");
                        customSerial.println(count);
                        if (count > 100)
                        {
                            customSerial.println("Restart ESP");
                            delay(500);
                            ESP.restart();
                        }
                    }
                }
            }
            else if (mode == 2)
            {
                if (Ethernet.begin(mac))
                {
                    customSerial.println("DHCP OK!");
                }
                else
                {
                    customSerial.println("Failed to configure Ethernet using DHCP");
                    if (Ethernet.hardwareStatus() == EthernetNoHardware)
                    {
                        customSerial.println("Ethernet shield was not found.");
                        while (true)
                        {
                            delay(500);
                            ESP.restart();
                        }
                    }
                    if (Ethernet.linkStatus() == LinkOFF)
                    {
                        customSerial.println("Ethernet cable is not connected.");
                    }
                }
                digitalWrite(W5500_CS, HIGH);
            }
            delay(2000);
        }
    }
}
```

### Penjelasan Fungsi Reconnect:

1. Inisialisasi:
   - `bcount`: Menghitung percobaan koneksi
   - Loop berjalan selama client tidak terkoneksi

2. Logika Koneksi:
   - Mencoba koneksi MQTT maksimal 10 kali
   - Jika gagal, ESP akan di-restart
   - Menggunakan client_id dan kredensial yang telah ditentukan

3. Mode Koneksi:
   - Mode 1: Koneksi WiFi
     * Mencoba koneksi WiFi
     * Maksimal 100 kali percobaan
     * Restart jika gagal

   - Mode 2: Koneksi Ethernet
     * Konfigurasi DHCP
     * Pengecekan hardware Ethernet
     * Pengecekan koneksi kabel

4. Error Handling:
   - Pesan error spesifik untuk setiap kegagalan
   - Restart otomatis jika diperlukan
   - Delay 2 detik antara percobaan

## 10. Local Time Function
```cpp
void LocalTime()
{
    struct tm timeinfo;
    if (!getLocalTime(&timeinfo))
    {
        customSerial.println("Failed to obtain time");
        return;
    }
    strftime(TimeNow, 30, "%H:%M:%S %x", &timeinfo);
    customSerial.println(TimeNow);
}
```

### Penjelasan Fungsi LocalTime:

1. Komponen:
   - `struct tm timeinfo`: Struktur untuk menyimpan informasi waktu
   - `getLocalTime()`: Fungsi untuk mendapatkan waktu lokal
   - `strftime()`: Memformat waktu ke string

2. Format Waktu:
   - `%H:%M:%S`: Format jam (jam:menit:detik)
   - `%x`: Format tanggal sesuai locale

3. Error Handling:
   - Pesan error jika gagal mendapatkan waktu
   - Return kosong jika terjadi kegagalan

## 11. Save Log Function
```cpp
void saveLog()
{
    for (const SensorData &entry : node_data)
    {
        LocalTime();
        char tmp[10];
        if (entry.temp < 0)
            sprintf(tmp, "-%d", (int)entry.temp);
        else
            sprintf(tmp, "%d", (int)entry.temp);

        sprintf(dataLogJson, "{"
                             "\"ID\": %d, "
                             "\"Timestamp\": \"%s\", "
                             "\"Iradian\": %d, "
                             "\"WindSpeed\": %d.%02d, "
                             "\"WindDirection\": %d.%02d, "
                             "\"Temperature\": %s.%02d, "
                             "\"RelativeHumidity\": %d.%02d, "
                             "\"AtmosphericPressure\": %d, "
                             "\"Rainfall\": %d.%02d"
                             "},\n",
                entry.id,
                TimeNow,
                entry.iradian,
                (int)entry.wspeed, abs((int)(entry.wspeed * 100) % 100),
                (int)entry.wdirect, abs((int)(entry.wdirect * 100) % 100),
                tmp, abs((int)(entry.temp * 100) % 100),
                (int)entry.hum, abs((int)(entry.hum * 100) % 100),
                entry.atmp,
                (int)entry.rain, abs((int)(entry.rain * 100) % 100));
        
        customSerial.println("Saving data from ID-" + String(entry.id) + "...");
        appendFile(SD, "/log_receiver.txt", dataLogJson);
    }
}
```

### Penjelasan Fungsi saveLog:

1. Format Data:
   - Iterasi melalui setiap entry dalam node_data
   - Mengambil timestamp saat ini
   - Format khusus untuk temperatur negatif

2. Format JSON:
   - Data disimpan dalam format JSON
   - Mencakup semua parameter sensor
   - Format angka desimal XX.XX

3. Penyimpanan:
   - File tujuan: "/log_receiver.txt"
   - Menggunakan fungsi appendFile
   - Menambahkan koma dan newline di akhir

## 12. Send Data Function
```cpp
void sendData()
{
    if (!client.connected())
    {
        reconnect();
    }

    client.loop();

    char data_send[40];
    String topic = "";
    for (const SensorData &entry : node_data)
    {
        customSerial.print("Send data for ID: ");
        customSerial.println(entry.id);

        topic = TOPIC_IR + String(entry.id);
        sprintf(data_send, "%d", entry.iradian);
        client.publish(topic.c_str(), data_send);

        topic = TOPIC_WS + String(entry.id);
        sprintf(data_send, "%d.%02d", (int)entry.wspeed, abs((int)(entry.wspeed * 100) % 100));
        client.publish(topic.c_str(), data_send);

        topic = TOPIC_WD + String(entry.id);
        sprintf(data_send, "%d.%02d", (int)entry.wdirect, abs((int)(entry.wdirect * 100) % 100));
        client.publish(topic.c_str(), data_send);

        char tmp[10];
        if (entry.temp < 0)
            sprintf(tmp, "-%d", (int)entry.temp);
        else
            sprintf(tmp, "%d", (int)entry.temp);
            
        topic = TOPIC_TEMP + String(entry.id);
        sprintf(data_send, "%s.%02d", tmp, abs((int)(entry.temp * 100) % 100));
        client.publish(topic.c_str(), data_send);

        topic = TOPIC_HUM + String(entry.id);
        sprintf(data_send, "%d.%02d", (int)entry.hum, abs((int)(entry.hum * 100) % 100));
        client.publish(topic.c_str(), data_send);

        topic = TOPIC_ATMP + String(entry.id);
        sprintf(data_send, "%d", entry.atmp);
        client.publish(topic.c_str(), data_send);

        topic = TOPIC_RAIN + String(entry.id);
        sprintf(data_send, "%d.%02d", (int)entry.rain, abs((int)(entry.rain * 100) % 100));
        client.publish(topic.c_str(), data_send);

        customSerial.println("Data sent!");
    }
}
```

### Penjelasan Fungsi sendData:

1. Koneksi MQTT:
   - Pengecekan koneksi
   - Reconnect jika terputus
   - Menjalankan client loop

2. Format Data:
   - Buffer 40 karakter untuk data
   - Topic dinamis sesuai ID node
   - Format desimal dengan 2 digit

3. Pengiriman Data:
   - Iterasi untuk setiap sensor
   - Publish ke topic yang sesuai
   - Konfirmasi pengiriman

4. Error Handling:
   - Konversi string ke c_str()
   - Penanganan nilai negatif
   - Format angka desimal
