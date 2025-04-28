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

```cpp
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
// OLED pins
#define OLED_SDA 21
#define OLED_SCL 22
#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64

#include "SD.h"
#include "FS.h"
#define SD_CS 13
#define SD_SCK 14
#define SD_MISO 2
#define SD_MOSI 15

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
