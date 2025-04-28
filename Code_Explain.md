# HEADER
#### DEV_ID 
DEV_ID adalah nomor unik yang diberikan kepada setiap node
```cpp
int DEV_ID = 15; 
```

#### WiFi Configuration
```cpp
char ssid[32] = "HCORP MOBILE"; 
char password[32] = "password123"; 
```
#### SSID 
    SSID (Set Service Identifier) adalah nama jaringan WiFi yang digunakan untuk mengindenttifikasi jaringan tertentu.
Beberapa poin penting tentang SSID:
- SSID dapat terdiri dari hingga 32 karakter
- SSID bersifat case-sensitive (peka huruf besar/kecil)
- SSID biasanya ditetapkan oleh pembuat router secara default, tetapi dapat diubah oleh pengguna
- Setiap jaringan WiFi memiliki SSID unik untuk membedakannya dari jaringan lain

SSID berfungsi sebagai "nama panggilan" untuk jaringan WiFi Anda dan merupakan cara perangkat mengidentifikasi jaringan mana yang ingin mereka sambungkan.


```cpp
#include <WiFi.h>
```
WiFi.h adalah library yang digunakan untuk menghubungkan ESP32 ke jaringan WiFi. 

#### Ethernet Configuration
```cpp
#include <Ethernet.h>
#define W5500_CS 25
``` 
Kode di atas adalah untuk mengatur koneksi Ethernet menggunakan modul W5500. 
1. **W5500_CS** adalah pin chip select untuk modul W5500. Pin ini digunakan untuk mengontrol komunikasi SPI dengan modul W5500. 
2. **25** adalah pin yang digunakan untuk mengontrol komunikasi SPI dengan modul W5500.

##### Pin SPI CS
**Dalam komunikasi SPI**, setiap perangkat slave memiliki **pin CS (Chip Select)** yang digunakan untuk mengatur perangkat mana yang aktif pada saat itu.  
- Gunakan **logika LOW** untuk mengaktifkan perangkat.  
- Gunakan **logika HIGH** untuk menonaktifkan perangkat.  
- Hanya satu perangkat yang dapat aktif pada satu waktu melalui **pin CS masing-masing**.

#### Serial Configuration
```cpp
HardwareSerial customSerial(1);
```
ESP32 memiliki beberapa port serial (UART). Port serial default (Serial) biasanya digunakan untuk komunikasi dengan komputer (upload program, debug, dll). 

Agar tidak terjadi **konflik**, maka port serial 1 (customSerial) dibuat untuk komunikasi dengan modul LoRa.

#### SPI Configuration
```cpp
#include <SPI.h>
#include <LoRa.h>
#define SCK 5
#define MISO 19
#define MOSI 27
#define SS 18
#define RST 23
#define DIO0 26
// 433E6 for Asia
// 866E6 for Europe
// 915E6 for North America
#define BAND 920E6
```

Pin Deskripsi:
1. **SCK** (Serial Clock): Pin clock SPI, digunakan untuk sinkronisasi data. Diatur ke pin GPIO 5.
2. **MISO** (Master In Slave Out): Pin untuk menerima data dari modul LoRa ke mikrokontroler. Diatur ke pin GPIO 19.
3. **MOSI** (Master Out Slave In): Pin untuk mengirim data dari mikrokontroler ke modul LoRa. Diatur ke pin GPIO 27.
4. **SS** (Slave Select) atau CS (Chip Select): Pin untuk memilih modul LoRa sebagai perangkat SPI yang aktif. Diatur ke pin GPIO 18.
5. **RST** (Reset): Pin untuk mereset modul LoRa. Diatur ke pin GPIO 23.
6. **DIO0** (Digital I/O 0): Pin interrupt yang digunakan oleh modul LoRa untuk memberi sinyal bahwa data telah diterima atau siap dikirim. Diatur ke pin GPIO 26. Nanti dipakai dengan library LoRa.setPins(SS, RST, DIO0) untuk mengatur pin-pin tersebut. Nanti dipakai dengan library LoRa.begin(BAND) untuk memulai komunikasi LoRa.
7. **BAND**: Frekuensi yang digunakan untuk komunikasi LoRa. Diatur ke 920E6 untuk North America.

#### I2C Configuration
```cpp
#include <Wire.h>
```
Wire.h adalah library yang digunakan untuk komunikasi I2C (Inter-Integrated Circuit) pada ESP32. Library ini memungkinkan Anda untuk berkomunikasi dengan perangkat I2C seperti sensor, modul, dan layar OLED.

#### OLED Configuration
```cpp 
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
```
**Adafruit_GFX.h** adalah library dasar untuk menggambar grafik pada layar OLED, sedangkan **Adafruit_SSD1306.h** adalah library khusus untuk mengendalikan layar OLED berbasis SSD1306 (driver untuk layar OLED yang menggunakan chip SSD1306). Library ini menyediakan fungsi-fungsi untuk menggambar teks, gambar, dan bentuk pada layar OLED.

#### OLED Pin Configuration
```cpp
// OLED pins
#define OLED_SDA 21
#define OLED_SCL 22
#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
```
1. **OLED_SDA**: Pin data untuk komunikasi I2C. Diatur ke pin GPIO 21.
2. **OLED_SCL**: Pin clock untuk komunikasi I2C. Diatur ke pin GPIO 22.
3. **SCREEN_WIDTH**: Lebar layar OLED dalam piksel. Diatur ke 128 piksel.
4. **SCREEN_HEIGHT**: Tinggi layar OLED dalam piksel. Diatur ke 64 piksel.

#### SD Card Configuration
```cpp
#include "SD.h"
#include "FS.h"
#define SD_CS 13
#define SD_SCK 14
#define SD_MISO 2
#define SD_MOSI 15
```

1. SD.h: Library untuk mengakses kartu SD.
2. FS.h: Library untuk sistem file pada ESP32.
3. SD_CS: Pin chip select untuk kartu SD. Diatur ke pin GPIO 13.
4. SD_SCK: Pin clock untuk komunikasi SPI dengan kartu SD. Diatur ke pin GPIO 14.  
5. SD_MISO: Pin untuk menerima data dari kartu SD ke mikrokontroler. Diatur ke pin GPIO 2.
6. SD_MOSI: Pin untuk mengirim data dari mikrokontroler ke kartu SD. Diatur ke pin GPIO 15.

#### Library SD.h

**SD.h** adalah library yang digunakan untuk mengakses kartu SD pada ESP32. Library ini menyediakan antarmuka untuk membaca, menulis, membuat, dan menghapus file di kartu SD. Dengan menggunakan library ini, Anda dapat menyimpan data seperti log, konfigurasi, atau file lainnya ke kartu SD.

##### Fitur Utama:
1. **Membaca File**: Anda dapat membaca isi file yang tersimpan di kartu SD.
2. **Menulis File**: Anda dapat menambahkan data ke file yang ada atau membuat file baru.
3. **Menghapus File**: File yang tidak diperlukan dapat dihapus untuk mengosongkan ruang.
4. **Membuat Direktori**: Anda dapat membuat struktur folder untuk mengorganisasi file.
5. **Kompatibilitas Sistem File**: Mendukung sistem file FAT16 dan FAT32.

##### Catatan Penting:
- Pastikan kartu SD diformat dengan sistem file FAT16 atau FAT32.
- Gunakan modul pembaca kartu SD yang kompatibel dengan ESP32.
- Periksa koneksi pin SPI untuk memastikan komunikasi yang stabil.

##### Contoh Penggunaan:
```cpp
#include "SD.h"
#include "FS.h"

// Pin untuk kartu SD
#define SD_CS 13

void setup() {
    Serial.begin(115200);

    // Inisialisasi kartu SD
    if (!SD.begin(SD_CS)) 
        Serial.println("Kartu SD gagal diinisialisasi!");
        return;
    }
    Serial.println("Kartu SD berhasil diinisialisasi.");

    // Membuat file baru dan menulis data
    File file = SD.open("/example.txt", FILE_WRITE);
    if (file) {
        file.println("Hello, SD card!");
        file.close();
        Serial.println("Data berhasil ditulis ke file.");
    } else {
        Serial.println("Gagal membuka file untuk menulis.");
    }

    // Membaca isi file
    file = SD.open("/example.txt");
    if (file) {
        Serial.println("Isi file:");
        while (file.available()) {
            Serial.write(file.read());
        }
        file.close();
    } else {
        Serial.println("Gagal membuka file untuk membaca.");
    }
}

void loop() {
    // Tidak ada kode di loop
}
```

##### Penjelasan Pin:
1. **SD_CS**: Pin chip select untuk kartu SD. Diatur ke GPIO 13.
2. **SD_SCK**: Pin clock untuk komunikasi SPI.
3. **SD_MISO**: Pin untuk menerima data dari kartu SD.
4. **SD_MOSI**: Pin untuk mengirim data ke kartu SD.

##### Catatan Penting:
- Pastikan kartu SD diformat dengan sistem file FAT16 atau FAT32.
- Gunakan modul pembaca kartu SD yang kompatibel dengan ESP32.
- Periksa koneksi pin SPI untuk memastikan komunikasi yang stabil.

Dengan library **SD.h**, Anda dapat dengan mudah mengelola data pada kartu SD untuk berbagai aplikasi seperti logging data sensor, menyimpan konfigurasi, atau menyimpan file lainnya.


```cpp
// LoRaSPI
SPIClass LoraSpi(HSPI);

// Switching PIN
#define SW1 34
#define SW2 35

// Select button
#define SELECT_BUTTON 4
int mode = 0;

Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

//======== Timing ========
unsigned long currentMillis;
unsigned long previousMillisRead = 0;
unsigned long previousAckMillisRead = 0;
unsigned long previousPublishMillisRead = 0;

// Global Variable
float wspeed, wdirect, temp, hum, rain;
int iradian, atmp;

// Append log data to SDCard
void appendFile(fs::FS &fs, const char *path, const char *message)
{
    customSerial.printf("Appending to file: %s\n", path);

    File file = fs.open(path, FILE_APPEND);
    if (!file)
    {
        // If the file doesn't exist, create it
        file = fs.open(path, FILE_WRITE);
        if (!file)
        {
            customSerial.println("Failed to create or open file");
            display.fillRect(0, 50, 128, 40, SSD1306_BLACK);
            display.display();
            display.setCursor(0, 55);
            display.print("Failed to write log");
            display.display();
            return;
        }
    }

    if (file.print(message))
    {
        customSerial.println("Message appended");
    }
    else
    {
        display.fillRect(0, 50, 128, 40, SSD1306_BLACK);
        display.display();
        display.setCursor(0, 55);
        display.print("Failed to write log");
        display.display();
        customSerial.println("Append failed");
    }

    file.close();
}

// restart if init module failed
void restartDevice(String Message, int delay_s)
{
    customSerial.println(Message);

    display.setCursor(0, 10);
    display.print(Message);
    display.display();
    customSerial.println("Restart in : ");
    for(int i=delay_s; i>=0; i--){
      display.fillRect(0, 50, 128, 30, SSD1306_BLACK);
      display.setCursor(0, 55);
      display.print("      Restart in " + String(i) + "s");
      display.display();
      customSerial.print(" " + String(i));
      delay(1000);
    }
    display.clearDisplay();
    display.display();
    ESP.restart();
}
