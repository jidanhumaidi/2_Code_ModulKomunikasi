# HEADER

## 1. DEV_ID 
### 1.1 Penjelasan
DEV_ID adalah nomor unik yang diberikan kepada setiap node.

```cpp
int DEV_ID = 15; 
```

## 2. WiFi Configuration
### 2.1 Konfigurasi WiFi
```cpp
char ssid[32] = "HCORP MOBILE"; 
char password[32] = "password123"; 
```

## 3. SSID 
### 3.1 Penjelasan
SSID (Set Service Identifier) adalah nama jaringan WiFi yang digunakan untuk mengidentifikasi jaringan tertentu.

### 3.2 Poin Penting tentang SSID
1. SSID dapat terdiri dari hingga 32 karakter.
2. SSID bersifat case-sensitive (peka huruf besar/kecil).
3. SSID biasanya ditetapkan oleh pembuat router secara default, tetapi dapat diubah oleh pengguna.
4. Setiap jaringan WiFi memiliki SSID unik untuk membedakannya dari jaringan lain.

SSID berfungsi sebagai "nama panggilan" untuk jaringan WiFi Anda dan merupakan cara perangkat mengidentifikasi jaringan mana yang ingin mereka sambungkan.

```cpp
#include <WiFi.h>
```

WiFi.h adalah library yang digunakan untuk menghubungkan ESP32 ke jaringan WiFi.

## 4. Ethernet Configuration
### 4.1 Konfigurasi Ethernet
```cpp
#include <Ethernet.h>
#define W5500_CS 25
```

### 4.2 Penjelasan
1. **W5500_CS** adalah pin chip select untuk modul W5500. Pin ini digunakan untuk mengontrol komunikasi SPI dengan modul W5500.
2. **25** adalah pin yang digunakan untuk mengontrol komunikasi SPI dengan modul W5500.

### 4.3 Pin SPI CS
**Dalam komunikasi SPI**, setiap perangkat slave memiliki **pin CS (Chip Select)** yang digunakan untuk mengatur perangkat mana yang aktif pada saat itu.
- Gunakan **logika LOW** untuk mengaktifkan perangkat.
- Gunakan **logika HIGH** untuk menonaktifkan perangkat.
- Hanya satu perangkat yang dapat aktif pada satu waktu melalui **pin CS masing-masing**.

## 5. Serial Configuration
### 5.1 Konfigurasi Serial
```cpp
HardwareSerial customSerial(1);
```

ESP32 memiliki beberapa port serial (UART). Port serial default (Serial) biasanya digunakan untuk komunikasi dengan komputer (upload program, debug, dll). Agar tidak terjadi **konflik**, maka port serial 1 (customSerial) dibuat untuk komunikasi dengan modul LoRa.

## 6. SPI Configuration
### 6.1 Konfigurasi SPI
```cpp
#include <SPI.h>
#include <LoRa.h>
#define SCK 5
#define MISO 19
#define MOSI 27
#define SS 18
#define RST 23
#define DIO0 26
#define BAND 920E6
```

### 6.2 Penjelasan Pin
1. **SCK** (Serial Clock): Pin clock SPI, digunakan untuk sinkronisasi data. Diatur ke pin GPIO 5.
2. **MISO** (Master In Slave Out): Pin untuk menerima data dari modul LoRa ke mikrokontroler. Diatur ke pin GPIO 19.
3. **MOSI** (Master Out Slave In): Pin untuk mengirim data dari mikrokontroler ke modul LoRa. Diatur ke pin GPIO 27.
4. **SS** (Slave Select) atau CS (Chip Select): Pin untuk memilih modul LoRa sebagai perangkat SPI yang aktif. Diatur ke pin GPIO 18.
5. **RST** (Reset): Pin untuk mereset modul LoRa. Diatur ke pin GPIO 23.
6. **DIO0** (Digital I/O 0): Pin interrupt yang digunakan oleh modul LoRa untuk memberi sinyal bahwa data telah diterima atau siap dikirim. Diatur ke pin GPIO 26.
7. **BAND**: Frekuensi yang digunakan untuk komunikasi LoRa. Diatur ke 920E6 untuk North America.

## 7. I2C Configuration
### 7.1 Konfigurasi I2C
```cpp
#include <Wire.h>
```

Wire.h adalah library yang digunakan untuk komunikasi I2C (Inter-Integrated Circuit) pada ESP32. Library ini memungkinkan Anda untuk berkomunikasi dengan perangkat I2C seperti sensor, modul, dan layar OLED.

## 8. OLED Configuration
### 8.1 Library OLED
```cpp 
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
```

**Adafruit_GFX.h** adalah library dasar untuk menggambar grafik pada layar OLED, sedangkan **Adafruit_SSD1306.h** adalah library khusus untuk mengendalikan layar OLED berbasis SSD1306.

### 8.2 Konfigurasi Pin OLED
```cpp
#define OLED_SDA 21
#define OLED_SCL 22
#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
```

1. **OLED_SDA**: Pin data untuk komunikasi I2C. Diatur ke pin GPIO 21.
2. **OLED_SCL**: Pin clock untuk komunikasi I2C. Diatur ke pin GPIO 22.
3. **SCREEN_WIDTH**: Lebar layar OLED dalam piksel. Diatur ke 128 piksel.
4. **SCREEN_HEIGHT**: Tinggi layar OLED dalam piksel. Diatur ke 64 piksel.

## 9. SD Card Configuration
### 9.1 Library SD Card
```cpp
#include "SD.h"
#include "FS.h"
#define SD_CS 13
#define SD_SCK 14
#define SD_MISO 2
#define SD_MOSI 15
```

### 9.2 Penjelasan Pin
1. **SD_CS**: Pin chip select untuk kartu SD. Diatur ke GPIO 13.
2. **SD_SCK**: Pin clock untuk komunikasi SPI.
3. **SD_MISO**: Pin untuk menerima data dari kartu SD.
4. **SD_MOSI**: Pin untuk mengirim data ke kartu SD.

### 9.3 Fitur Utama Library SD.h
1. Membaca File.
2. Menulis File.
3. Menghapus File.
4. Membuat Direktori.
5. Kompatibilitas Sistem File FAT16 dan FAT32.

### 9.4 Contoh Penggunaan
```cpp
#include "SD.h"
#include "FS.h"

#define SD_CS 13

void setup() {
    Serial.begin(115200);

    if (!SD.begin(SD_CS)) {
        Serial.println("Kartu SD gagal diinisialisasi!");
        return;
    }
    Serial.println("Kartu SD berhasil diinisialisasi.");
}
```

## 10. LoRa SPI Configuration
### 10.1 Konfigurasi LoRa SPI
```cpp
SPIClass LoraSpi(HSPI);
```

Kode di atas digunakan untuk membuat instance SPI khusus untuk modul LoRa menggunakan hardware SPI (HSPI) pada ESP32.

### 10.2 Penjelasan Kelas dan Fungsi
#### Kelas
Kelas mendefinisikan atribut (data) dan metode (fungsi) yang dapat digunakan untuk membuat objek.

```cpp
class MyClass {
    public:
        int myAttribute;
        void myMethod() {
            // Kode untuk metode
        }
};
```

#### Fungsi
Fungsi adalah blok kode yang dirancang untuk melakukan tugas tertentu.

```cpp
#define SW1 34
#define SW2 35
```

### 10.3 Restart Device
```cpp
void restartDevice(String Message, int delay_s) {
    customSerial.println(Message);
    ESP.restart();
}
```
