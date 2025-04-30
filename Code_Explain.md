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

1. SPIClass itu library bawaan dari ESP32 yang digunakan untuk komunikasi SPI.
2. LoraSpi itu nama objek dari kelas SPIClass buat bikin jalur komunikasi SPI.
3. HSPI itu mode SPI yang digunakan pada ESP32. Ada dua mode SPI di ESP32: VSPI dan HSPI. HSPI adalah SPI hardware kedua yang ada di ESP32.


### 10.2 Kelas
Kelas itu seperti design rumah yang siap untuk dibangun (objek). 
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
1. **MyClass** adalah nama kelas.
2. **myAttribute** adalah atribut dari kelas yang menyimpan data.
3. **myMethod()** adalah metode dari kelas yang berisi kode yang dapat dijalankan.
4. **public** adalah akses modifier yang menentukan visibilitas atribut dan metode. Dalam hal ini, atribut dan metode dapat diakses dari luar kelas.
5. **void** adalah tipe pengembalian dari metode, yang berarti tidak mengembalikan nilai apa pun.

Maka bila kita ingin gunakan kelas diatas kita tinggal menuliskan:
```cpp
MyClass myObject; // Membuat objek dari kelas MyClass
myObject.myAttribute = 10; // Mengakses atribut
```
Maka myAttribute punya nilai 10 sekarang.

## 11. Switching dan Select Button Configuration
```cpp
// Switching PIN
#define SW1 34
#define SW2 35

// Select button
#define SELECT_BUTTON 4
int mode = 0;
```
int mode = 0; adaalah variabel untuk mode saat ini.

## 12. Fungsi Setup
```cpp
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);
```

Kode diatas membuat objek display dari kelas Adafruit_SSD1306 dengan lebar 128 piksel dan tinggi 64 piksel. Parameter -1 menunjukkan bahwa tidak ada pin reset yang digunakan .&Wire adalah objek I2C yang digunakan untuk komunikasi dengan layar OLED. Maksud dari & adalah untuk mengirimkan alamat dari objek Wire ke dalam konstruktor Adafruit_SSD1306. 
- & adalah operator yang digunakan untuk mendapatkan alamat dari variabel. 
- Operator ini memungkinkan kita untuk mengakses lokasi memori dari variabel yang bersangkutan, sehingga kita dapat mengoperasikan objek dengan lebih efisien.
- Dengan menggunakan &Wire, kita memberi tahu konstruktor Adafruit_SSD1306 bahwa kita ingin menggunakan objek Wire yang sudah ada untuk komunikasi I2C.

## 13. Timing
```cpp
unsigned long currentMillis;
```
CurrentMillis adalah variabel untuk menyimpan waktu saat ini dalam milidetik. Ini digunakan untuk menghitung waktu yang telah berlalu sejak program dimulai.

```cpp
unsigned long previousMillisRead = 0;
```


```cpp
unsigned long previousAckMillisRead = 0;
```

```cpp
unsigned long previousPublishMillisRead = 0;
```

## 14. Unsgined Long
Unsigned long adalah tipe data yang digunakan untuk menyimpan angka bulat positif. Tipe data ini memiliki rentang yang lebih besar dibandingkan dengan tipe data long biasa, karena tidak menyimpan nilai negatif. Unsigned long biasanya digunakan untuk menghitung waktu, seperti dalam kasus ini, di mana kita menghitung waktu dalam milidetik. Jarak maksimal yang dapat disimpan dalam unsigned long adalah 0 hingga 4.294.967.295 (2^32 - 1).

## 15. Global Variable
```cpp
// Global Variable
float wspeed, wdirect, temp, hum, rain;
int iradian, atmp;
```
wspeed, wdirect, temp, hum, rain menggunakan float karena nilai yang disimpan adalah desimal.
iradian dan atmp menggunakan int karena nilai yang disimpan adalah bilangan bulat.

## 16. Fungsi AppendFile atau menambah file

```cpp
// Append log data to SDCard
```cpp
void appendFile(fs::FS &fs, const char *path, const char *message)
{
    // Menampilkan pesan bahwa file sedang ditambahkan
    customSerial.printf("Appending to file: %s\n", path);

    // Membuka file dalam mode append (menambahkan data di akhir file)
    File file = fs.open(path, FILE_APPEND);
    if (!file)
    {
        // Jika file tidak ada, buat file baru dalam mode write
        file = fs.open(path, FILE_WRITE);
        if (!file)
        {
            // Jika gagal membuat atau membuka file, tampilkan pesan error
            customSerial.println("Failed to create or open file");
            display.fillRect(0, 50, 128, 40, SSD1306_BLACK); // Membersihkan area layar OLED
            display.display();
            display.setCursor(0, 55); // Mengatur posisi kursor di layar OLED
            display.print("Failed to write log"); // Menampilkan pesan error di layar OLED
            display.display();
            return; // Keluar dari fungsi
        }
    }

    // Menambahkan pesan ke file
    if (file.print(message))
    {
        // Jika berhasil menambahkan pesan, tampilkan pesan sukses
        customSerial.println("Message appended");
    }
    else
    {
        // Jika gagal menambahkan pesan, tampilkan pesan error
        display.fillRect(0, 50, 128, 40, SSD1306_BLACK); // Membersihkan area layar OLED
        display.display();
        display.setCursor(0, 55); // Mengatur posisi kursor di layar OLED
        display.print("Failed to write log"); // Menampilkan pesan error di layar OLED
        display.display();
        customSerial.println("Append failed");
    }

    // Menutup file setelah selesai
    file.close();
}
```

### Penjelasan Baris per Baris

```cpp 
void appendFile(fs::FS &fs, const char *path, const char *message)
```
Fungsi ini digunakan untuk menambahkan data ke dalam file yang ada di sistem file (FS). Parameter:  
   - `fs::FS &fs`: Referensi ke sistem file yang digunakan (misalnya, SD card). (Meminjam sistem file (FS) dari namespace fs untuk digunakan dalam operasi file.) 
        - fs itu librarynya
        - FS itu class yang ada di library fs.h
        - :: itu kayak simbol buat akses ke class atau fungsi yang ada di dalam library.
        - & itu simbol untuk referensi, jadi kita bisa pakai objek fs tanpa bikin salinan baru.
        - &fs itu artinya kita mau pakai objek fs yang udah ada, bukan bikin objek baru.  
   - `const char *path`: Path atau lokasi file yang akan ditambahkan.  
        - `const` (constanta) itu artinya adalah nilai yang tidak dapat diubah setelah ditetapkan.
        - `char` itu tipe data untuk menyimpan karakter.
        - `*` itu artinya adalah pointer, yang menunjuk ke alamat memori dari karakter pertama dalam string. Misal log.txt, maka *path menunjuk ke huruf 'l' di log.txt. Sehingga kita bisa mengakses string tersebut dengan menggunakan pointer. Digunakan agar kita bisa mengakses nama file tanpa harus menyalin seluruh string ke dalam memori.

   - `const char *message`: Pesan yang akan ditambahkan ke file.

2. **customSerial.printf("Appending to file: %s\n", path);**  
   Menampilkan pesan di serial monitor bahwa file sedang ditambahkan, dengan mencetak path file.

3. **File file = fs.open(path, FILE_APPEND);**  
   Membuka file dalam mode append. Jika file tidak ada, akan mengembalikan nilai false.

4. **if (!file)**  
   Mengecek apakah file berhasil dibuka. Jika tidak, masuk ke blok berikutnya.

5. **file = fs.open(path, FILE_WRITE);**  
   Jika file tidak ditemukan, coba buat file baru dalam mode write.

6. **if (!file)**  
   Jika file tetap gagal dibuat, tampilkan pesan error di serial monitor dan layar OLED.

7. **display.fillRect(0, 50, 128, 40, SSD1306_BLACK);**  
   Membersihkan area layar OLED di posisi tertentu.

8. **display.setCursor(0, 55);**  
   Mengatur posisi kursor di layar OLED untuk menampilkan pesan.

9. **display.print("Failed to write log");**  
   Menampilkan pesan error di layar OLED.

10. **if (file.print(message))**  
    Mencoba menambahkan pesan ke file. Jika berhasil, tampilkan pesan sukses.

11. **customSerial.println("Message appended");**  
    Menampilkan pesan sukses di serial monitor.

12. **else**  
    Jika gagal menambahkan pesan, tampilkan pesan error di serial monitor dan layar OLED.

13. **file.close();**  
    Menutup file untuk memastikan data tersimpan dengan benar.

## 15. Fungsi Restart Device
### 15. Fungsi Restart Device

```cpp
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
```

#### Penjelasan Baris per Baris

1. **`void restartDevice(String Message, int delay_s)`**  
   Fungsi ini digunakan untuk merestart perangkat dengan menampilkan pesan tertentu di layar OLED dan serial monitor.  
   - `String Message`: Pesan yang akan ditampilkan sebelum perangkat restart.  
   - `int delay_s`: Waktu tunda (dalam detik) sebelum perangkat restart.

2. **`customSerial.println(Message);`**  
   Menampilkan pesan ke serial monitor.

3. **`display.setCursor(0, 10);`**  
   Mengatur posisi kursor di layar OLED pada koordinat (0, 10).

4. **`display.print(Message);`**  
   Menampilkan pesan pada layar OLED.

5. **`display.display();`**  
   Memperbarui layar OLED agar pesan yang ditampilkan terlihat.

6. **`customSerial.println("Restart in : ");`**  
   Menampilkan teks "Restart in :" di serial monitor.

7. **`for(int i=delay_s; i>=0; i--)`**  
   Loop untuk menghitung mundur waktu tunda sebelum restart.

8. **`display.fillRect(0, 50, 128, 30, SSD1306_BLACK);`**  
   Membersihkan area layar OLED di posisi tertentu dengan warna hitam.

9. **`display.setCursor(0, 55);`**  
   Mengatur posisi kursor di layar OLED untuk menampilkan waktu hitung mundur.

10. **`display.print("      Restart in " + String(i) + "s");`**  
    Menampilkan teks "Restart in Xs" di layar OLED, di mana `X` adalah nilai hitung mundur.

11. **`display.display();`**  
    Memperbarui layar OLED agar hitung mundur terlihat.

12. **`customSerial.print(" " + String(i));`**  
    Menampilkan nilai hitung mundur di serial monitor.

13. **`delay(1000);`**  
    Menunggu selama 1 detik sebelum melanjutkan iterasi berikutnya.

14. **`display.clearDisplay();`**  
    Membersihkan seluruh layar OLED setelah hitung mundur selesai.

15. **`display.display();`**  
    Memperbarui layar OLED agar terlihat kosong.

16. **`ESP.restart();`**  
    Merestart perangkat menggunakan fungsi bawaan ESP32.

#### Fungsi Utama
Fungsi ini berguna untuk memberikan waktu kepada pengguna untuk melihat pesan kesalahan atau informasi sebelum perangkat melakukan restart. Hitung mundur ditampilkan di layar OLED dan serial monitor untuk memberikan indikasi visual dan tekstual.
