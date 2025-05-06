# Main
## 1. Liabraries
```cpp
#include "Header.h"
#include "TransmitterMode.h"
#include "ReceiverMode.h"
```

## 2. Fungsi SaveLog
```cpp
void saveLog() {
  for (const SensorData &entry : node_data) {
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
### 2.1 Fungsi SaveLog
Fungsi ini digunakan untuk menyimpan data sensor ke dalam file log di SD card. Fungsi ini akan melakukan iterasi pada setiap entri data sensor yang ada di dalam vector `node_data`, kemudian mengonversi data tersebut menjadi format JSON dan menyimpannya ke dalam file log.
```cpp
void saveLog() {}
```

### 2.2 Iterasi Data Sensor
```cpp
  for (const SensorData &entry : node_data) {
    LocalTime();
    char tmp[10];
    //.....
  }
```
For loop digunakan untuk melakukan iterasi pada `vector node_data` yang berisi kumpulan data sensor. Sintaks yang digunakan adalah range-based for loop di C++, dengan format:

```cpp
for (const SensorData &entry : node_data)
```

Dimana:
- `const SensorData &entry`: Mendefinisikan variabel referensi konstan bernama `entry` bertipe `SensorData`
- `node_data`: Container/vector yang akan diiterasi
- Setiap iterasi, `entry` akan mereferensikan elemen berikutnya dalam `node_data`

Menggunakan referensi (`&`) membantu menghindari penyalinan data yang tidak perlu, meningkatkan efisiensi program.

`LocalTime();` digunakan untuk mendapatkan waktu lokal saat ini. Fungsi ini akan mengupdate variabel `TimeNow` yang berisi waktu saat ini.

`char tmp[10];` mendeklarasikan array karakter `tmp` untuk menyimpan string yang akan digunakan untuk menyimpan nilai suhu. Kenapa hanya variabel `tmp` yang dideklarasikan? Karena hanya variabel ini yang akan digunakan untuk menyimpan string sementara, sedangkan variabel lainnya sudah dideklarasikan sebelumnya di bagian awal program yaitu di bagian `Header.h` yakni `dataLogJson` dan `dataSendJson`.
`10` adalah panjang maksimum string yang akan disimpan dalam `tmp`. Panjang ini harus cukup untuk menampung nilai suhu yang akan disimpan. Kenapa `10`? Karena suhu maksimum yang mungkin terjadi adalah **100 derajat** celcius, dan kita perlu menambahkan karakter `-` untuk suhu negatif. Jadi panjang maksimum yang dibutuhkan adalah 10 karakter. 

#### 2.2.1 Loop Iteration
For loop adalah struktur kontrol yang memungkinkan kode dijalankan berulang kali dengan jumlah iterasi tertentu. Beberapa format penulisan for loop:

1. Format Tradisional:
```cpp
for (inisialisasi; kondisi; increment) {
  // kode yang diulang
}
```

2. Range-based Format (C++11):
```cpp
for (deklarasi : range) {
  // kode yang diulang
}
```

3. Format Infinite Loop:
```cpp
for (;;) {
  // loop tanpa henti
}
```

Bagian-bagian for loop:
- Inisialisasi: Menentukan nilai awal counter
- Kondisi: Menentukan kapan loop berhenti
- Increment: Mengubah nilai counter setiap iterasi

#### 2.2.2 Karakter
Karakter adalah unit terkecil dari teks, seperti huruf, angka, atau simbol. Dalam konteks ini, `char tmp[10]` artinya:

Array karakter dengan panjang 10 slot, seperti kotak-kotak berikut:
```cpp
[0][1][2][3][4][5][6][7][8][9]
```

Contoh pengisian untuk suhu -15.5°C:
```cpp
['-'][1][5][.][5]['\0'][ ][ ][ ][ ]
```

- Setiap kotak bisa diisi 1 karakter
- Karakter `\0` menandai akhir string
- 10 slot cukup untuk menyimpan nilai suhu (termasuk tanda minus dan desimal)

### 2.3 Format String
```cpp
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
```

### 2.3.1 Format Suhu
```cpp
if (entry.temp < 0)
  sprintf(tmp, "-%d", (int)entry.temp);
else
  sprintf(tmp, "%d", (int)entry.temp);
```
Kode ini menangani formatting nilai suhu:
- Mengecek apakah suhu negatif
- Menggunakan `sprintf()` untuk memformat suhu ke string
- Menyimpan hasilnya ke array `tmp`
- entry.temp adalah nilai suhu yang diambil dari data sensor
- `(int)entry.temp` mengonversi suhu ke integer (menghilangkan desimal), kenapa? Karena kita hanya ingin menampilkan bagian bulat dari suhu, dan kita akan menangani desimal di bagian lain yaitu di bagian `sprintf(dataLogJson, ...)`
- `sprintf(tmp, "-%d", (int)entry.temp);` menyimpan suhu negatif ke dalam `tmp` dengan format `-XX` (misal -15)

#### 2.3.1.1 Sintaksis If-Else
If-else adalah struktur kontrol yang memungkinkan program mengeksekusi kode berbeda berdasarkan kondisi:

```cpp
if (kondisi) {
  // kode jika kondisi true
} else {
  // kode jika kondisi false
}
```

Contoh logika:
1. Single If:
```cpp
if (x > 0) {
  // eksekusi jika x positif
}
```

2. If-Else:
```cpp
if (x > 0) {
  // eksekusi jika x positif
} else {
  // eksekusi jika x tidak positif
}
```

3. If-Else If:
```cpp
if (x > 0) {
  // eksekusi jika x positif
} else if (x < 0) {
  // eksekusi jika x negatif
} else {
  // eksekusi jika x nol
}
```

Catatan:
- Kondisi harus bernilai boolean (true/false)
- Kurung kurawal opsional untuk blok kode tunggal
- Dapat menggunakan operator logika (&&, ||, !)

### 2.3.2 Format JSON
```cpp
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
                         ....)
```
Fungsi `sprintf()` memformat data ke string JSON dengan:
- Format specifiers:
  - `%d` - integer (ID, Iradian, AtmosphericPressure)
  - `%s` - string (Timestamp)
  - `%d.%02d` - desimal 2 digit (WindSpeed, WindDirection, Temperature, RelativeHumidity, Rainfall)

#### 2.3.2.1 Sprintf
`sprintf()` adalah fungsi C++ untuk memformat string dengan sintaks:
```cpp
sprintf(buffer, "format_string", arg1, arg2, ...);
```

Komponen:
- `buffer`: Array karakter tujuan
- `format_string`: Template string dengan format specifiers
- `arg1, arg2, ...`: Nilai yang akan dimasukkan

Format Specifiers:
- `%d` - Integer
- `%f` - Float
- `%s` - String
- `%c` - Karakter
- `%.2f` - Float 2 desimal
- `%02d` - Integer 2 digit dengan leading zero

Contoh:
```cpp
char buffer[50];
int id = 1;
float temp = 23.45;
sprintf(buffer, "ID:%d Temp:%.1f", id, temp);
// Hasil: "ID:1 Temp:23.4"
```

Logika:
1. Alokasi buffer
2. Define format string
3. Masukkan nilai ke placeholder
4. Hasil disimpan ke buffer

### 2.3.3 Konversi Data
Untuk data desimal:
```cpp
(int)entry.wspeed               // Bagian integer
abs((int)(entry.wspeed * 100) % 100)  // 2 digit desimal
```
- Menggunakan type casting ke integer
- Perkalian 100 untuk mengambil 2 digit desimal
- Modulo 100 untuk membatasi 2 digit
- `abs()` untuk nilai absolut

### 2.3.4 Matrikulasi yang Diperlukan

1. Dasar Pemrograman C++:
   - Type casting
   - Pointer dan array
   - Fungsi dan parameter
   - Operator aritmatika

2. Format String & sprintf:
   - Format specifiers (%d, %s, dll)
   - Buffer management
   - String formatting

3. Floating Point:
   - Representasi floating point
   - Konversi float ke integer
   - Presisi desimal

4. JSON:
   - Format dan struktur JSON
   - Escape characters
   - JSON objects dan arrays

5. Matematika:
   - Modulo operation
   - Nilai absolut
   - Konversi desimal

6. Contoh Kalkulasi:
```cpp
float nilai = 23.456;
int integer = (int)nilai;            // = 23
int desimal = abs((int)(nilai * 100) % 100); // = 45
```

7. Memory Management:
   - Stack vs Heap
   - Buffer overflow prevention
   - String buffer sizing

#### 2.3.5 Menyimpan Data ke SD Card
```cpp
    customSerial.println("Saving data from ID-" + String(entry.id) + "...");
    appendFile(SD, "/log_receiver.txt", dataLogJson);
```
```customSerial.println("Saving data from ID-" + String(entry.id) + "...");``` secara sintaksis ini berarti kita menggabungkan string dengan ID dari data sensor yang sedang disimpan. Fungsi `String()` digunakan untuk mengonversi ID ke dalam format string agar bisa digabungkan dengan string lainnya.

```appendFile(SD, "/log_receiver.txt", dataLogJson);``` adalah fungsi yang digunakan untuk menyimpan data ke dalam file log di SD card. Fungsi ini akan membuka file `/log_receiver.txt` dan menambahkan data JSON yang telah diformat sebelumnya ke dalam file tersebut. 

## 3. Fungsi Setup
```cpp
void setup() {
  //initialize customSerial Monitor
  // customSerial.begin(9600);
  customSerial.begin(9600, SERIAL_8N1, 0, 1);

  pinMode(SW1, INPUT);
  pinMode(SW2, INPUT);
  pinMode(SELECT_BUTTON, INPUT);
  
  //initialize OLED
  Wire.begin(OLED_SDA, OLED_SCL);
  if(!display.begin(SSD1306_SWITCHCAPVCC, 0x3c, false, false)) { // Address 0x3C for 128x32
    customSerial.println(F("SSD1306 allocation failed"));
    for(;;); // Don't proceed, loop forever
  }

  display.clearDisplay();
  display.setTextColor(WHITE);
  display.setTextSize(1);
  display.setCursor(0,0);

  //SPI LoRa pins
  LoRa.setSPI(LoraSpi);
  LoraSpi.begin(SCK, MISO, MOSI, SS);
  //setup LoRa transceiver module
  LoRa.setPins(SS, RST, DIO0);
  
  if (!LoRa.begin(BAND)) {
    restartDevice("Starting LoRa Failed", 5);
    while (1);
  }

  // initialize SD Card
  pinMode(SD_CS, OUTPUT);
  digitalWrite(SD_CS, HIGH);
  // setup SDCard SPI
  SPI.begin(SD_SCK , SD_MISO, SD_MOSI);
  SPI.setClockDivider(SPI_CLOCK_DIV2); // Set the SPI clock speed
  delay(100);
  bool bypass = !digitalRead(SELECT_BUTTON);
  if(!bypass){
    customSerial.println("Reading SDCard ...");
    if (!SD.begin(SD_CS)) {
      restartDevice("Card Mount Failed!\n\n  Hold select button \n    to run system \n   without SDCard", 5);
      customSerial.println("Card Mount Failed");
    }
    else {
      customSerial.println("Card Mounted!!");
      uint8_t cardType = SD.cardType();
      if (cardType == CARD_NONE) {
        customSerial.println("No SD card attached");
      } else {
        customSerial.print("SD Card Type: ");
        if (cardType == CARD_MMC) {
          customSerial.println("MMC");
        } else if (cardType == CARD_SD) {
          customSerial.println("SDSC");
        } else if (cardType == CARD_SDHC) {
          customSerial.println("SDHC");
        } else {
          customSerial.println("UNKNOWN");
        }
        File configFile = SD.open("/config.ini", FILE_READ);
        if (!configFile) {
          customSerial.println("Failed to open config file");
          return;
        }
      
        int idNumber = 0;
      
        // Read each line from the file
        while (configFile.available()) {
          String configLine = configFile.readStringUntil('\n');
      
          // Extract values from each line
          if (configLine.startsWith("ID:")) {
            DEV_ID = configLine.substring(configLine.indexOf(':') + 1, configLine.indexOf(';')).toInt();
          } else if (configLine.startsWith("SSID:")) {
            configLine.substring(configLine.indexOf(':') + 2, configLine.length() - 3).toCharArray(ssid, sizeof(ssid));
          } else if (configLine.startsWith("PASS:")) {
            configLine.substring(configLine.indexOf(':') + 2, configLine.length() - 3).toCharArray(password, sizeof(password));
          }
        }
      
        configFile.close();
      
        customSerial.println("Read configuration from config.ini:");
        customSerial.print("ID: ");
        customSerial.println(DEV_ID);
        customSerial.print("SSID: ");
        customSerial.println(ssid);
        customSerial.print("Password: ");
        customSerial.println(password);
      }
    }
  }
  else {    
    display.clearDisplay();
    display.setCursor(0, 10);
    display.print("Skip Reading SDCard..");
    display.display();
    customSerial.println("Reading SDCard skipped");
  }
  
  display.clearDisplay();
  display.setCursor(0,0);
  
  node.begin(slaveID, customSerial);
  if(digitalRead(SW1)){
    mode = 0;
    customSerial.println("MODE TRANSMITTER");
    display.print("ID-" + String(DEV_ID));
    display.setCursor(60,0);
    display.println("TRANSMITTER");
    display.display();
  }
  else if(digitalRead(SW2)){
    mode = 1;
    customSerial.print("Connecting to WiFi");
    display.println("Connecting to WiFi..");
    display.println();
    display.println("SSID:" + String(ssid));
    display.println("Pass:" + String(password)); 
    display.display();
    
    // Connect to Wi-Fi
    WiFi.begin(ssid, password);
    while (WiFi.status() != WL_CONNECTED) {
      customSerial.print(".");
      delay(1000);
    }

    customSerial.println("WiFi Connected");
    
    //connecting to a mqtt broker
    client.setClient(wifiClient);
    client.setServer(mqtt_server, mqtt_port);

    configTime(gmtOffset_sec, daylightOffset_sec, ntpServer);
    LocalTime();
  
    customSerial.println("MODE RECEIVER/WIFI");

    display.clearDisplay();
    display.setCursor(0,5);
    display.println("  MODE RECEIVER/WIFI");
    display.display();
  }
  else{
    mode = 2;
    
    customSerial.print("Connecting to LAN");
    display.println("Connecting to LAN..");
    display.display();
    
    Ethernet.init(W5500_CS);

    if (Ethernet.begin(mac)) { // Dynamic IP setup
      customSerial.println("DHCP OK!");
    } else {
      customSerial.println("Failed to configure Ethernet using DHCP");
      // Check for Ethernet hardware present
      if (Ethernet.hardwareStatus() == EthernetNoHardware) {
        customSerial.println("Ethernet shield was not found.  Sorry, can't run without hardware. :(");
        while (true) {
          delay(500); // do nothing, no point running without Ethernet hardware
          ESP.restart();
        }
      }
      if (Ethernet.linkStatus() == LinkOFF) {
        customSerial.println("Ethernet cable is not connected.");
      }
    }
    digitalWrite(W5500_CS, HIGH);
    
    //connecting to a mqtt broker
    client = PubSubClient(ethClient);
    client.setServer(mqtt_server, mqtt_port);
    while (!client.connected()) {
      customSerial.printf("The client %s connects to the public mqtt broker\n", client_id);
      if (client.connect("RECEIVER", mqtt_username, mqtt_password)) {
        customSerial.println("MQTT broker connected");
      } else {
        customSerial.print("failed with state ");
        customSerial.print(client.state());
        delay(2000);
      }
    }

    configTime(gmtOffset_sec, daylightOffset_sec, ntpServer);
    LocalTime();
  
    customSerial.println("MODE RECEIVER/LAN");

    display.clearDisplay();
    display.setCursor(0,5);
    display.println("   MODE RECEIVER/LAN");
    display.display();
  }
  if(mode == 0)
    setData();
}
```

### 3.1 Fungsi Setup
```cpp
void setup() {}
```
Fungsi `setup()` adalah fungsi yang dijalankan sekali saat perangkat dinyalakan. Fungsi ini digunakan untuk menginisialisasi berbagai komponen dan pengaturan awal sistem.

### 3.2 Inisialisasi Serial Monitor
```cpp
  //initialize customSerial Monitor
  // customSerial.begin(9600);
  customSerial.begin(9600, SERIAL_8N1, 0, 1);
```
- `customSerial.begin(9600, SERIAL_8N1, 0, 1);` digunakan untuk menginisialisasi komunikasi serial dengan baud rate 9600 bps. 
- Parameter `SERIAL_8N1` menunjukkan format data 8 bit, 1 stop bit, dan tidak ada parity. 
- `0` dan `1` adalah pin RX dan TX yang digunakan untuk komunikasi serial.

### 3.3 Inisialisasi Pin
```cpp
  pinMode(SW1, INPUT);
  pinMode(SW2, INPUT);
  pinMode(SELECT_BUTTON, INPUT);
```
- `pinMode(SW1, INPUT);` mengatur pin `SW1` sebagai input.
+ `pinMode(SW2, INPUT);` mengatur pin `SW2` sebagai input.
+ `pinMode(SELECT_BUTTON, INPUT);` mengatur pin `SELECT_BUTTON` sebagai input.

###  3.4 Inisialisasi OLED
```cpp
  //initialize OLED
  Wire.begin(OLED_SDA, OLED_SCL);
  if(!display.begin(SSD1306_SWITCHCAPVCC, 0x3c, false, false)) { // Address 0x3C for 128x32
    customSerial.println(F("SSD1306 allocation failed"));
    for(;;); // Don't proceed, loop forever
  }
```
- `Wire.begin(OLED_SDA, OLED_SCL);` menginisialisasi komunikasi I2C untuk OLED dengan pin SDA dan SCL yang ditentukan.
- `display.begin(SSD1306_SWITCHCAPVCC, 0x3c, false, false)` menginisialisasi tampilan OLED dengan alamat I2C `0x3C` dan mode switching capacitor. Jika gagal, program akan mencetak pesan kesalahan dan masuk ke loop tak terbatas.
- `customSerial.println(F("SSD1306 allocation failed"));` mencetak pesan kesalahan ke serial monitor jika alokasi tampilan gagal.
- `for(;;);` adalah loop tak terbatas yang menghentikan eksekusi program jika tampilan gagal diinisialisasi. Berguna untuk mencegah program berjalan tanpa tampilan yang berfungsi.

### 3.5 Display Clear
```cpp
  display.clearDisplay();
  display.setTextColor(WHITE);
  display.setTextSize(1);
  display.setCursor(0,0);
```
- `display.clearDisplay();` membersihkan tampilan OLED.
- `display.setTextColor(WHITE);` mengatur warna teks menjadi putih.
- `display.setTextSize(1);` mengatur ukuran teks menjadi 1.
- `display.setCursor(0,0);` mengatur posisi kursor tampilan ke koordinat (0,0).

### 3.6 Inisialisasi LoRa
```cpp
  //SPI LoRa pins
  LoRa.setSPI(LoraSpi);
  LoraSpi.begin(SCK, MISO, MOSI, SS);
  //setup LoRa transceiver module
  LoRa.setPins(SS, RST, DIO0);
  ```
- `LoRa.setSPI(LoraSpi);` mengatur SPI untuk modul LoRa.  
- `LoraSpi.begin(SCK, MISO, MOSI, SS);` menginisialisasi SPI dengan pin SCK, MISO, MOSI, dan SS yang ditentukan.
- `LoRa.setPins(SS, RST, DIO0);` mengatur pin untuk modul LoRa, di mana `SS` adalah pin chip select, `RST` adalah pin reset, dan `DIO0` adalah pin interrupt.

### 3.7 Inisialisasi LoRa Transceiver
```cpp
  if (!LoRa.begin(BAND)) {
    restartDevice("Starting LoRa Failed", 5);
    while (1);
  }
```
- `if (!LoRa.begin(BAND))` memeriksa apakah modul LoRa berhasil diinisialisasi dengan frekuensi `BAND`. Jika gagal, fungsi `restartDevice()` akan dipanggil untuk merestart perangkat dengan pesan kesalahan "Starting LoRa Failed".
- `while (1);` adalah loop tak terbatas yang menghentikan eksekusi program jika inisialisasi LoRa gagal. Ini berguna untuk mencegah program berjalan tanpa modul LoRa yang berfungsi. Perulangan while tanpa kondisi yang akan selalu bernilai true sehingga program tidak akan pernah keluar dari loop ini.

### 3.8 Inisialisasi SD Card
```cpp
  // initialize SD Card
  pinMode(SD_CS, OUTPUT);
  digitalWrite(SD_CS, HIGH);
```
- `pinMode(SD_CS, OUTPUT);` mengatur pin `SD_CS` sebagai output untuk chip select SD card.
- `digitalWrite(SD_CS, HIGH);` mengatur pin `SD_CS` ke HIGH untuk memastikan SD card tidak aktif saat inisialisasi.

### 3.9 Setup SD Card SPI
```cpp
  // setup SDCard SPI
  SPI.begin(SD_SCK , SD_MISO, SD_MOSI);
  SPI.setClockDivider(SPI_CLOCK_DIV2); // Set the SPI clock speed
  delay(100);
```
- `SPI.begin(SD_SCK , SD_MISO, SD_MOSI);` menginisialisasi SPI untuk SD card dengan pin SCK, MISO, dan MOSI yang ditentukan.
- `SPI.setClockDivider(SPI_CLOCK_DIV2);` mengatur kecepatan clock SPI menjadi setengah dari kecepatan maksimum. Yaitu `SPI_CLOCK_DIV2` yang bernilai 2, sehingga kecepatan clock SPI adalah `F_CPU / 2` sama dengan `8 MHz` pada ESP32. Digunakan agar komunikasi SPI lebih stabil dan tidak terjadi kesalahan saat membaca atau menulis data ke SD card. `16MHz` bisa lebih cepat, tapi kadang-kadang bisa menyebabkan kesalahan saat membaca atau menulis data ke SD card. Jadi lebih baik menggunakan kecepatan yang lebih rendah untuk memastikan komunikasi SPI yang stabil. 
- `delay(100);` memberikan jeda selama 100 ms untuk memastikan SPI siap sebelum melanjutkan.

### 3.10 Bypass SD Card
```cpp
  bool bypass = !digitalRead(SELECT_BUTTON);
  if(!bypass){
    customSerial.println("Reading SDCard ...");
    if (!SD.begin(SD_CS)) {
      restartDevice("Card Mount Failed!\n\n  Hold select button \n    to run system \n   without SDCard", 5);
      customSerial.println("Card Mount Failed");
    }
  }
```
- `bool bypass = !digitalRead(SELECT_BUTTON);` membaca status tombol `SELECT_BUTTON`. Jika tombol ditekan (LOW), maka `bypass` akan bernilai true.
- `if(!bypass){` memeriksa apakah bypass tidak aktif (tombol tidak ditekan). Jika bypass aktif, maka program akan melewati pembacaan SD card.
- `customSerial.println("Reading SDCard ...");` mencetak pesan ke serial monitor bahwa pembacaan SD card sedang dilakukan.
- `if (!SD.begin(SD_CS))` memeriksa apakah SD card berhasil diinisialisasi. Jika gagal, fungsi `restartDevice()` akan dipanggil untuk merestart perangkat dengan pesan kesalahan "Card Mount Failed".
- `restartDevice("Card Mount Failed!\n\n  Hold select button \n    to run system \n   without SDCard", 5);` adalah fungsi yang digunakan untuk merestart perangkat dengan pesan kesalahan. Pesan ini akan ditampilkan di serial monitor dan di layar OLED. Fungsi ini juga akan menunggu selama 5 detik sebelum merestart perangkat. Pesan ini memberikan instruksi kepada pengguna untuk menekan tombol select untuk menjalankan sistem tanpa SD card.
- `customSerial.println("Card Mount Failed");` mencetak pesan kesalahan ke serial monitor jika pembacaan SD card gagal.

### 3.11 Pembacaan SD Card
```cpp
    else {
      customSerial.println("Card Mounted!!");
      uint8_t cardType = SD.cardType();
      if (cardType == CARD_NONE) {
        customSerial.println("No SD card attached");
      } else {
        customSerial.print("SD Card Type: ");
        if (cardType == CARD_MMC) {
          customSerial.println("MMC");
        } else if (cardType == CARD_SD) {
          customSerial.println("SDSC");
        } else if (cardType == CARD_SDHC) {
          customSerial.println("SDHC");
        } else {
          customSerial.println("UNKNOWN");
        }
```
- `else {` jika pembacaan SD card berhasil, maka program akan melanjutkan ke bagian ini.
- `customSerial.println("Card Mounted!!");` mencetak pesan bahwa SD card berhasil dipasang. 
- `uint8_t cardType = SD.cardType();` mendapatkan tipe SD card yang terpasang.
- `if (cardType == CARD_NONE)` memeriksa apakah tidak ada SD card yang terpasang. Jika tidak ada, maka program akan mencetak pesan kesalahan.
- `customSerial.print("SD Card Type: ");` mencetak tipe SD card yang terpasang.
- `if (cardType == CARD_MMC)` memeriksa apakah tipe SD card adalah MMC. Jika ya, maka program akan mencetak "MMC".
- `else if (cardType == CARD_SD)` memeriksa apakah tipe SD card adalah SDSC. Jika ya, maka program akan mencetak "SDSC".
- `else if (cardType == CARD_SDHC)` memeriksa apakah tipe SD card adalah SDHC. Jika ya, maka program akan mencetak "SDHC".
- `else` jika tipe SD card tidak dikenali, maka program akan mencetak "UNKNOWN".

### 3.12 Membaca File Konfigurasi
```cpp
        File configFile = SD.open("/config.ini", FILE_READ);
        if (!configFile) {
          customSerial.println("Failed to open config file");
          return;
        }
```
- `File configFile = SD.open("/config.ini", FILE_READ);` membuka file konfigurasi `config.ini` dari SD card untuk dibaca.
- `if (!configFile)` memeriksa apakah file konfigurasi berhasil dibuka. Jika gagal, maka program akan mencetak pesan kesalahan dan keluar dari fungsi.
- `customSerial.println("Failed to open config file");` mencetak pesan kesalahan ke serial monitor jika file konfigurasi gagal dibuka.
- `return;` keluar dari fungsi jika file konfigurasi gagal dibuka.

### 3.12.1 Deklarasi Variabel
```cpp
        int idNumber = 0;
```
- `int idNumber = 0;` mendeklarasikan variabel `idNumber` untuk menyimpan ID dari data yang dibaca dari file konfigurasi yaitu file `config.ini`. Variabel ini diinisialisasi dengan nilai 0.

### 3.13 Membaca Setiap Baris dari File
```cpp
        // Read each line from the file
        while (configFile.available()) {
          String configLine = configFile.readStringUntil('\n');
```
- `while (configFile.available())` memeriksa apakah masih ada data yang tersedia untuk dibaca dari file konfigurasi.
- `String configLine = configFile.readStringUntil('\n');` membaca setiap baris dari file konfigurasi hingga karakter newline (`\n`) dan menyimpannya ke dalam variabel `configLine`. Variabel ini bertipe `String` yang merupakan tipe data string di Arduino.
- `configLine` akan berisi satu baris dari file konfigurasi setiap kali loop dijalankan.
- `configFile.readStringUntil('\n')` adalah fungsi yang membaca data dari file hingga karakter newline dan mengembalikan hasilnya sebagai string.
- Fungsi ini akan terus membaca hingga tidak ada lagi data yang tersedia di file.
- `configFile.available()` memeriksa apakah masih ada data yang tersedia untuk dibaca dari file konfigurasi.
- Jika ada, maka loop akan terus berjalan dan membaca baris berikutnya.
- Jika tidak ada data yang tersedia, maka loop akan berhenti.

### 3.14 Ekstraksi Nilai dari Setiap Baris
```cpp
          // Extract values from each line
          if (configLine.startsWith("ID:")) {
            DEV_ID = configLine.substring(configLine.indexOf(':') + 1, configLine.indexOf(';')).toInt();
          } 
```
- `if (configLine.startsWith("ID:"))` memeriksa apakah baris dimulai dengan "ID:". Jika ya, maka program akan mengekstrak ID dari baris tersebut.
- `DEV_ID = configLine.substring(configLine.indexOf(':') + 1, configLine.indexOf(';')).toInt();` mengekstrak ID dari baris konfigurasi dengan mengambil substring dari karakter setelah ":" hingga karakter ";" dan mengonversinya menjadi integer. Hasilnya disimpan ke dalam variabel `DEV_ID`.
    - `configLine.startsWith("ID:")` memeriksa apakah baris dimulai dengan "ID:".
     - `configLine.indexOf(':')` menemukan posisi karakter ":" dalam string.
    - `:` itulah karakter pemisah antara nama dan nilai. contoh "ID: 1234" maka `configLine.indexOf(':')` akan mengembalikan posisi karakter ":".
    - `configLine.indexOf(';')` menemukan posisi karakter ";" dalam string. Contoh "ID: 1234;" maka `configLine.indexOf(';')` akan mengembalikan posisi karakter ";". Dikembalikan pada fungsi `substring()` untuk mengekstrak nilai ID. Sehingga hasilnya adalah "1234".
    - .toInt() mengonversi substring menjadi integer.

```cpp
else if (configLine.startsWith("SSID:")) {
            configLine.substring(configLine.indexOf(':') + 2, configLine.length() - 3).toCharArray(ssid, sizeof(ssid));
          }
```
- `else if (configLine.startsWith("SSID:"))` memeriksa apakah baris dimulai dengan "SSID:". Jika ya, maka program akan mengekstrak SSID dari baris tersebut.
- `configLine.substring(configLine.indexOf(':') + 2, configLine.length() - 3).toCharArray(ssid, sizeof(ssid));` mengekstrak SSID dari baris konfigurasi dengan mengambil substring dari karakter setelah ":" hingga karakter ";" dan mengonversinya menjadi karakter array. Hasilnya disimpan ke dalam variabel `ssid`.
    - `configLine.indexOf(':') + 2` untuk mendapatkan posisi karakter setelah ":".
    - `configLine.length() - 3` untuk mendapatkan panjang string dikurangi 3 karakter terakhir (misalnya "\r\n").
    - `.toCharArray(ssid, sizeof(ssid));` mengonversi substring menjadi karakter array dan menyimpannya ke dalam variabel `ssid`.

```cpp
else if (configLine.startsWith("PASS:")) {
            configLine.substring(configLine.indexOf(':') + 2, configLine.length() - 3).toCharArray(password, sizeof(password));
          }
```
- `configLine.substring(configLine.indexOf(':') + 2, configLine.length() - 3).toCharArray(password, sizeof(password));` mengekstrak password dari baris konfigurasi dengan cara yang sama seperti SSID. Hasilnya disimpan ke dalam variabel `password`.
    - `configLine.indexOf(':') + 2` untuk mendapatkan posisi karakter setelah ":".
    - `configLine.length() - 3` untuk mendapatkan panjang string dikurangi 3 karakter terakhir (misalnya "\r\n").
    - `.toCharArray(password, sizeof(password));` mengonversi substring menjadi karakter array dan menyimpannya ke dalam variabel `password`.

### 3.15 Menutup File Konfigurasi
```cpp
        configFile.close();
```
- `configFile.close();` menutup file konfigurasi setelah selesai membacanya. Ini penting untuk membebaskan sumber daya dan memastikan bahwa file tidak tetap terbuka.

### 3.16 Menampilkan Konfigurasi
```cpp
        customSerial.println("Read configuration from config.ini:");
        customSerial.print("ID: ");
        customSerial.println(DEV_ID);
        customSerial.print("SSID: ");
        customSerial.println(ssid);
        customSerial.print("Password: ");
        customSerial.println(password);
      }
    }
  }
```
- `customSerial.println("Read configuration from config.ini:");` mencetak pesan bahwa konfigurasi telah dibaca dari file `config.ini`.
- `customSerial.print("ID: ");` mencetak label "ID: " ke serial monitor.
- `customSerial.println(DEV_ID);` mencetak ID yang telah dibaca dari file konfigurasi.
- `customSerial.print("SSID: ");` mencetak label "SSID: " ke serial monitor.
- `customSerial.println(ssid);` mencetak SSID yang telah dibaca dari file konfigurasi.
- `customSerial.print("Password: ");` mencetak label "Password: " ke serial monitor.
- `customSerial.println(password);` mencetak password yang telah dibaca dari file konfigurasi.

### 3.17 Menampilkan Pesan Bypass
```cpp
  else {    
    display.clearDisplay();
    display.setCursor(0, 10);
    display.print("Skip Reading SDCard..");
    display.display();
    customSerial.println("Reading SDCard skipped");
  }
 ```
- `else {` jika bypass aktif (tombol ditekan), maka program akan melanjutkan ke bagian ini.
- `display.clearDisplay();` membersihkan tampilan OLED.
- `display.setCursor(0, 10);` mengatur posisi kursor tampilan ke koordinat (0,10).
- `display.print("Skip Reading SDCard..");` menampilkan pesan "Skip Reading SDCard.." di layar OLED.
- `display.display();` memperbarui tampilan OLED untuk menampilkan pesan yang telah ditulis.
- `customSerial.println("Reading SDCard skipped");` mencetak pesan bahwa pembacaan SD card telah dilewati ke serial monitor.

### 3.18 Menampilkan Pesan Awal
```cpp
  display.clearDisplay();
  display.setCursor(0,0);
```
- `display.clearDisplay();` membersihkan tampilan OLED.
- `display.setCursor(0,0);` mengatur posisi kursor tampilan ke koordinat (0,0).

### 3.19 Inisialisasi Node
```cpp
  node.begin(slaveID, customSerial);
  ```
- `node.begin(slaveID, customSerial);` menginisialisasi node dengan ID `slaveID` dan objek `customSerial` untuk komunikasi serial.
   - `node` adalah objek dari kelas `Node` yang digunakan untuk berkomunikasi dengan perangkat lain melalui protokol tertentu.
   - `slaveID` adalah ID dari node yang digunakan untuk mengidentifikasi perangkat ini dalam jaringan.
   - `customSerial` adalah objek yang digunakan untuk komunikasi serial dengan perangkat lain.

### 3.20 Memilih Mode
```cpp
  if(digitalRead(SW1)){
    mode = 0;
    customSerial.println("MODE TRANSMITTER");
    display.print("ID-" + String(DEV_ID));
    display.setCursor(60,0);
    display.println("TRANSMITTER");
    display.display();
  }
```
- `if(digitalRead(SW1)){` memeriksa apakah tombol `SW1` ditekan. Jika ya, maka mode diatur ke 0 (TRANSMITTER).
- `mode = 0;` mengatur mode ke 0, yang berarti perangkat akan berfungsi sebagai transmitter.
- `customSerial.println("MODE TRANSMITTER");` mencetak pesan bahwa mode yang dipilih adalah TRANSMITTER ke serial monitor.
- `display.print("ID-" + String(DEV_ID));` menampilkan ID perangkat di layar OLED.
- `display.setCursor(60,0);` mengatur posisi kursor tampilan ke koordinat (60,0).
- `display.println("TRANSMITTER");` menampilkan teks "TRANSMITTER" di layar OLED.
- `display.display();` memperbarui tampilan OLED untuk menampilkan pesan yang telah ditulis.
- `mode = 0;` mengatur mode ke 0, yang berarti perangkat akan berfungsi sebagai transmitter.
- `customSerial.println("MODE TRANSMITTER");` mencetak pesan bahwa mode yang dipilih adalah TRANSMITTER ke serial monitor.

### 3.21 Memilih Mode WiFi
```cpp
  else if(digitalRead(SW2)){
    mode = 1;
    customSerial.print("Connecting to WiFi");
    display.println("Connecting to WiFi..");
    display.println();
    display.println("SSID:" + String(ssid));
    display.println("Pass:" + String(password)); 
    display.display();
```
- `else if(digitalRead(SW2)){` memeriksa apakah tombol `SW2` ditekan. Jika ya, maka mode diatur ke 1 (RECEIVER/WIFI).
- `mode = 1;` mengatur mode ke 1, yang berarti perangkat akan berfungsi sebagai receiver dengan koneksi WiFi.
- `customSerial.print("Connecting to WiFi");` mencetak pesan bahwa perangkat sedang mencoba terhubung ke WiFi ke serial monitor.
- `display.println("Connecting to WiFi..");` menampilkan pesan "Connecting to WiFi.." di layar OLED.  
- `display.println();` menampilkan baris kosong di layar OLED.
- `display.println("SSID:" + String(ssid));` menampilkan SSID yang digunakan untuk koneksi WiFi di layar OLED.
- `display.println("Pass:" + String(password));` menampilkan password yang digunakan untuk koneksi WiFi di layar OLED.

### 3.22 Koneksi WiFi
```cpp
    // Connect to Wi-Fi
    WiFi.begin(ssid, password);
    while (WiFi.status() != WL_CONNECTED) {
      customSerial.print(".");
      delay(1000);
    }

    customSerial.println("WiFi Connected");
    
    //connecting to a mqtt broker
    client.setClient(wifiClient);
    client.setServer(mqtt_server, mqtt_port);

    configTime(gmtOffset_sec, daylightOffset_sec, ntpServer);
    LocalTime();
  
    customSerial.println("MODE RECEIVER/WIFI");

    display.clearDisplay();
    display.setCursor(0,5);
    display.println("  MODE RECEIVER/WIFI");
    display.display();
  }
```
- `WiFi.begin(ssid, password);` memulai koneksi WiFi dengan SSID dan password yang telah dibaca dari file konfigurasi.
- `while (WiFi.status() != WL_CONNECTED) {` memeriksa status koneksi WiFi. Jika status tidak terhubung, maka program akan mencetak "." ke serial monitor setiap detik hingga terhubung.
- `customSerial.print(".");` mencetak "." ke serial monitor setiap detik selama proses koneksi.
- `delay(1000);` memberikan jeda selama 1 detik sebelum memeriksa status koneksi lagi.
- `customSerial.println("WiFi Connected");` mencetak pesan bahwa koneksi WiFi berhasil ke serial monitor.
- `client.setClient(wifiClient);` mengatur objek `client` untuk menggunakan koneksi WiFi.
- `client.setServer(mqtt_server, mqtt_port);` mengatur server MQTT yang akan digunakan untuk koneksi.
- `configTime(gmtOffset_sec, daylightOffset_sec, ntpServer);` mengonfigurasi waktu dengan offset GMT dan daylight saving time menggunakan server NTP yang telah ditentukan.
- `LocalTime();` memanggil fungsi `LocalTime()` untuk mendapatkan waktu lokal.
- `customSerial.println("MODE RECEIVER/WIFI");` mencetak pesan bahwa mode yang dipilih adalah RECEIVER/WIFI ke serial monitor.
- `display.clearDisplay();` membersihkan tampilan OLED.
- `display.setCursor(0,5);` mengatur posisi kursor tampilan ke koordinat (0,5).
- `display.println("  MODE RECEIVER/WIFI");` menampilkan teks "MODE RECEIVER/WIFI" di layar OLED.
- `display.display();` memperbarui tampilan OLED untuk menampilkan pesan yang telah ditulis.

### 3.23 Memilih Mode LAN
```cpp
  else{
    mode = 2;
    
    customSerial.print("Connecting to LAN");
    display.println("Connecting to LAN..");
    display.display();
```
- `else{` jika tombol `SW2` tidak ditekan, maka mode diatur ke 2 (RECEIVER/LAN).
- `mode = 2;` mengatur mode ke 2, yang berarti perangkat akan berfungsi sebagai receiver dengan koneksi LAN.
- `customSerial.print("Connecting to LAN");` mencetak pesan bahwa perangkat sedang mencoba terhubung ke LAN ke serial monitor.
- `display.println("Connecting to LAN..");` menampilkan pesan "Connecting to LAN.." di layar OLED.
- `display.display();` memperbarui tampilan OLED untuk menampilkan pesan yang telah ditulis.

### 3.24 Koneksi LAN
```cpp
    Ethernet.init(W5500_CS);

    if (Ethernet.begin(mac)) { // Dynamic IP setup
      customSerial.println("DHCP OK!");
    } else {
      customSerial.println("Failed to configure Ethernet using DHCP");
      // Check for Ethernet hardware present
      if (Ethernet.hardwareStatus() == EthernetNoHardware) {
        customSerial.println("Ethernet shield was not found.  Sorry, can't run without hardware. :(");
        while (true) {
          delay(500); // do nothing, no point running without Ethernet hardware
          ESP.restart();
        }
      }
      if (Ethernet.linkStatus() == LinkOFF) {
        customSerial.println("Ethernet cable is not connected.");
      }
    }
    digitalWrite(W5500_CS, HIGH);
    
    //connecting to a mqtt broker
    client = PubSubClient(ethClient);
    client.setServer(mqtt_server, mqtt_port);
    while (!client.connected()) {
      customSerial.printf("The client %s connects to the public mqtt broker\n", client_id);
      if (client.connect("RECEIVER", mqtt_username, mqtt_password)) {
        customSerial.println("MQTT broker connected");
      } else {
        customSerial.print("failed with state ");
        customSerial.print(client.state());
        delay(2000);
      }
    }

    configTime(gmtOffset_sec, daylightOffset_sec, ntpServer);
    LocalTime();
  
    customSerial.println("MODE RECEIVER/LAN");

    display.clearDisplay();
    display.setCursor(0,5);
    display.println("   MODE RECEIVER/LAN");
    display.display();
  }
```
- `Ethernet.init(W5500_CS);` menginisialisasi Ethernet dengan pin chip select `W5500_CS`.
- `if (Ethernet.begin(mac))` memulai koneksi Ethernet dengan alamat MAC yang telah ditentukan. Jika berhasil, maka program akan mencetak pesan bahwa DHCP berhasil.
- `customSerial.println("DHCP OK!");` mencetak pesan bahwa DHCP berhasil ke serial monitor.
- `else {` jika koneksi Ethernet gagal, maka program akan melanjutkan ke bagian ini.
- `customSerial.println("Failed to configure Ethernet using DHCP");` mencetak pesan kesalahan bahwa konfigurasi Ethernet menggunakan DHCP gagal.
- `if (Ethernet.hardwareStatus() == EthernetNoHardware) {` memeriksa apakah perangkat keras Ethernet tidak ditemukan. Jika ya, maka program akan mencetak pesan kesalahan dan masuk ke loop tak terbatas.
- `customSerial.println("Ethernet shield was not found.  Sorry, can't run without hardware. :(");` mencetak pesan kesalahan bahwa perangkat keras Ethernet tidak ditemukan.
- `while (true) {` adalah loop tak terbatas yang menghentikan eksekusi program jika perangkat keras Ethernet tidak ditemukan. Ini berguna untuk mencegah program berjalan tanpa perangkat keras Ethernet yang berfungsi.
- `delay(500);` memberikan jeda selama 500 ms sebelum memeriksa status perangkat keras Ethernet lagi.
- `ESP.restart();` merestart perangkat jika perangkat keras Ethernet tidak ditemukan.
- `if (Ethernet.linkStatus() == LinkOFF) {` memeriksa apakah kabel Ethernet tidak terhubung. Jika ya, maka program akan mencetak pesan kesalahan.
- `customSerial.println("Ethernet cable is not connected.");` mencetak pesan kesalahan bahwa kabel Ethernet tidak terhubung.
- `digitalWrite(W5500_CS, HIGH);` mengatur pin chip select `W5500_CS` ke HIGH untuk memastikan Ethernet tidak aktif saat inisialisasi.
- `client = PubSubClient(ethClient);` menginisialisasi objek `client` untuk menggunakan koneksi Ethernet.
- `client.setServer(mqtt_server, mqtt_port);` mengatur server MQTT yang akan digunakan untuk koneksi.
- `while (!client.connected()) {` memeriksa apakah koneksi ke broker MQTT berhasil. Jika tidak, maka program akan mencetak pesan bahwa klien sedang mencoba terhubung ke broker MQTT.
- `customSerial.printf("The client %s connects to the public mqtt broker\n", client_id);` mencetak pesan bahwa klien sedang mencoba terhubung ke broker MQTT dengan ID klien yang telah ditentukan.
- `if (client.connect("RECEIVER", mqtt_username, mqtt_password)) {` mencoba untuk terhubung ke broker MQTT dengan username dan password yang telah ditentukan. Jika berhasil, maka program akan mencetak pesan bahwa koneksi berhasil.
- `customSerial.println("MQTT broker connected");` mencetak pesan bahwa koneksi ke broker MQTT berhasil.
- `else {` jika koneksi ke broker MQTT gagal, maka program akan melanjutkan ke bagian ini.
- `customSerial.print("failed with state ");` mencetak pesan bahwa koneksi ke broker MQTT gagal.
- `customSerial.print(client.state());` mencetak status kesalahan koneksi ke broker MQTT.
- `delay(2000);` memberikan jeda selama 2 detik sebelum mencoba terhubung lagi.
- `configTime(gmtOffset_sec, daylightOffset_sec, ntpServer);` mengonfigurasi waktu dengan offset GMT dan daylight saving time menggunakan server NTP yang telah ditentukan.
- `LocalTime();` memanggil fungsi `LocalTime()` untuk mendapatkan waktu lokal.
- `customSerial.println("MODE RECEIVER/LAN");` mencetak pesan bahwa mode yang dipilih adalah RECEIVER/LAN ke serial monitor.
- `display.clearDisplay();` membersihkan tampilan OLED.
- `display.setCursor(0,5);` mengatur posisi kursor tampilan ke koordinat (0,5).
- `display.println("   MODE RECEIVER/LAN");` menampilkan teks "MODE RECEIVER/LAN" di layar OLED.
- `display.display();` memperbarui tampilan OLED untuk menampilkan pesan yang telah ditulis.

### 3.24 Mode Trasmitter
```cpp
  if(mode == 0)
    setData();
```
- `if(mode == 0)` memeriksa apakah mode adalah TRANSMITTER. Jika ya, maka fungsi `setData()` akan dipanggil untuk mengatur data yang akan dikirim.
- `setData();` adalah fungsi yang digunakan untuk mengatur data yang akan dikirim oleh transmitter. Fungsi ini akan mengumpulkan data dari sensor dan mengonversinya menjadi format JSON sebelum mengirimnya melalui modul LoRa.
- Fungsi ini juga akan menyimpan data yang dikirim ke dalam file log di SD card.

## 4. Fungsi Loop
Fungsi loop adalah fungsi utama yang akan terus berjalan setelah fungsi setup selesai. Fungsi ini berfungsi untuk memproses data yang diterima dari LoRa dan mengirimkan data jika diperlukan.

```cpp
void loop() {
  currentMillis = millis();
  
  if(mode == 0){
    if (currentMillis - previousMillisRead >= 5000) {
      previousMillisRead = currentMillis;
      setData();
    }
    
    int packetSize = LoRa.parsePacket();
    if (packetSize) {
      //received a packet
      customSerial.print("Received packet ");
      //read packet
      while (LoRa.available()) {
        LoRaData = LoRa.readString();
      }
      if (LoRaData.startsWith("ACK#")) {
        customSerial.print("ACK Message Received : ");
        customSerial.println(LoRaData);
        // Remove "ACK," from the beginning of the string
        LoRaData.remove(0, 4);
      
        // Split the remaining string by commas
        int delimiterPos;
        ack_status = false;
        while ((delimiterPos = LoRaData.indexOf('#')) != -1) {
          String valueStr = LoRaData.substring(0, delimiterPos);
          int value = valueStr.toInt();
          LoRaData.remove(0, delimiterPos + 1);
      
          if (value == (int)DEV_ID) {
            customSerial.println("DEV_ID is found in the ACK message. Send Data Success!");
            ack_status = true;
            
            display.fillRect(0, 50, 128, 40, SSD1306_BLACK);
            display.setCursor(0, 55);
            display.print("Send data : -OK-");
            display.display();
          }
        }
        if(ack_status == false){
          customSerial.print("ACK data not found! ");
          if(waiting == false){
            int generateDelay = random(1000, 15000);
            randomDelay = currentMillis + generateDelay;
            waiting = true;
            customSerial.println("Resend data in " + String(generateDelay) + "s");
          }
        }
      }
    }
    if(ack_status == false){
      if(waiting){
        display.fillRect(0, 55, 128, 40, SSD1306_BLACK);
        display.setCursor(0, 55);
        display.println("Waiting " + String(randomDelay-currentMillis) + " ms");
        display.display();
        if(currentMillis >= randomDelay){
          customSerial.print("Waiting time over, sending data..");
          appendFile(SD, "/log_transmitter.txt", dataLogJson);
          LoRa.beginPacket();
          LoRa.print(dataSendJson);
          LoRa.endPacket();
          customSerial.print(dataLogJson);
          
          display.fillRect(0, 50, 128, 40, SSD1306_BLACK);
          display.display();
          display.setCursor(0, 55);
          display.println("Sending data..");
          display.display();
          
          waiting = false;
        }
      }
    }
  }
  
  if((mode == 1)||(mode == 2)){
    int packetSize = LoRa.parsePacket();
    if (packetSize) {
      //received a packet
      customSerial.print("Received packet ");
      //read packet
      while (LoRa.available()) {
        LoRaData = LoRa.readString();
      }
      customSerial.println(LoRaData);
      DynamicJsonDocument doc(1024);
      DeserializationError error = deserializeJson(doc, LoRaData);
      if (error) {
        customSerial.print("ERROR : Invalid Data - ");
        customSerial.println(error.c_str());
      } else {
        SensorData received_data;
        received_data.id = doc["ID"];
        received_data.iradian = doc["Ir"];
        received_data.wspeed = doc["WS"];
        received_data.wdirect = doc["WD"];
        received_data.temp = doc["Temp"];
        received_data.hum = doc["Hum"];
        received_data.atmp = doc["AP"];
        received_data.rain = doc["Rain"];
        
        String result = "ID : " + String(received_data.id) + "\n" + 
                 "Iradian : " + String(received_data.iradian) + "\n" + 
                 "WindSpeed : " + String(received_data.wspeed) + "\n" + 
                 "WindDirection : " + String(received_data.wdirect) + "\n" + 
                 "Temperature : " + String(received_data.temp) + "\n" + 
                 "Humidity : " + String(received_data.hum) + "\n" + 
                 "AtmosphericPressure : " + String(received_data.atmp) + "\n" + 
                 "Rainfall : " + String(received_data.rain);
        customSerial.println(result);
        bool is_collected = false;
        // Output the data to the customSerial port
        for (const SensorData& entry : node_data) {
          if(received_data.id == entry.id){
            is_collected = true;
            customSerial.println("Data From This Node is Already Collected");
          }
          customSerial.print("ID: ");
          customSerial.println(entry.id);
          display.fillRect(0, 45, 128, 10, SSD1306_BLACK);
          display.setCursor(0, 45);
          display.println("Received from " + String(entry.id));
          display.display();
        }
        if(!is_collected){
          // is it new data?
          node_data.push_back(received_data);
        }
      }
    }
    if (currentMillis - previousAckMillisRead >= DELAY_ACK) {
      previousAckMillisRead = currentMillis;
      ack_message = "ACK#";
      display.fillRect(0, 15, 128, 50, SSD1306_BLACK);
      display.setCursor(0, 15);
      display.print("Received(ID): ");
      for (const SensorData &entry : node_data)
      {
        ack_message += String(entry.id) + "#";
        display.print(String(entry.id) + ", ");
      }
      display.display();
      
      LoRa.beginPacket();
      LoRa.print(ack_message);
      LoRa.endPacket();
      delay(100);
      LoRa.beginPacket();
      LoRa.print(ack_message);
      LoRa.endPacket();
      delay(100);
      LoRa.beginPacket();
      LoRa.print(ack_message);
      LoRa.endPacket();
      
      customSerial.print("Sending ACK message : ");
      customSerial.println(ack_message);

      display.fillRect(50, 55, 128, 20, SSD1306_BLACK);
      display.setCursor(50, 55);
      display.println("ACK Sent");
      display.display();
    }
    if (currentMillis - previousPublishMillisRead >= INTERVAL_MQTT) {
      previousPublishMillisRead = currentMillis;
      // save log to sdcard
      saveLog();
      // send data with mqtt protocol
      sendData();
      // clear data
      node_data.clear();
    }
    
    int remaining_s = ((INTERVAL_MQTT - (currentMillis - previousPublishMillisRead))/1000);
    int remaining_m = remaining_s/60;
    remaining_s = remaining_s - (remaining_m*60);
    display.fillRect(0, 55, 128, 20, SSD1306_BLACK);
    display.setCursor(0, 55);
    display.println(String(remaining_m) + "m" + String(remaining_s) + "s");
    display.setCursor(55, 55);
    display.println("ACK in " + String((DELAY_ACK - (currentMillis - previousAckMillisRead))/1000) + "s");
    display.display();
    
  }
}
```

### 4.1 Fungsi Loop
```cpp
void loop() {
  currentMillis = millis();
```
- `void loop() {` mendeklarasikan fungsi loop yang akan terus berjalan setelah fungsi setup selesai.
- `currentMillis = millis();` menyimpan waktu saat ini dalam variabel `currentMillis`. Fungsi `millis()` mengembalikan jumlah milidetik sejak perangkat dinyalakan. Variabel ini digunakan untuk menghitung waktu yang telah berlalu.
- `currentMillis` adalah variabel yang menyimpan waktu saat ini dalam milidetik. Ini digunakan untuk menghitung waktu yang telah berlalu sejak perangkat dinyalakan.
- `millis()` adalah fungsi yang mengembalikan jumlah milidetik sejak perangkat dinyalakan. Fungsi ini digunakan untuk menghitung waktu yang telah berlalu.
- `currentMillis` akan diperbarui setiap kali fungsi loop dijalankan.

### 4.2 Mode Transmitter
```cpp
  if(mode == 0){
    if (currentMillis - previousMillisRead >= 5000) {
      previousMillisRead = currentMillis;
      setData();
    }
```
- `if(mode == 0){` memeriksa apakah mode adalah TRANSMITTER. Jika ya, maka program akan melanjutkan ke bagian ini.
- `if (currentMillis - previousMillisRead >= 5000) {` memeriksa apakah waktu yang telah berlalu sejak pembacaan terakhir lebih dari 5 detik. Jika ya, maka fungsi `setData()` akan dipanggil untuk mengatur data yang akan dikirim.
- `previousMillisRead = currentMillis;` memperbarui waktu pembacaan terakhir dengan waktu saat ini. Ini digunakan untuk menghitung waktu yang telah berlalu sejak pembacaan terakhir.
- `setData();` adalah fungsi yang digunakan untuk mengatur data yang akan dikirim oleh transmitter. Fungsi ini akan mengumpulkan data dari sensor dan mengonversinya menjadi format JSON sebelum mengirimnya melalui modul LoRa.
- Fungsi ini juga akan menyimpan data yang dikirim ke dalam file log di SD card.

### 4.3 Menerima Data LoRa
```cpp
    int packetSize = LoRa.parsePacket();
    if (packetSize) {
      //received a packet
      customSerial.print("Received packet ");
      //read packet
      while (LoRa.available()) {
        LoRaData = LoRa.readString();
     }
```
- `int packetSize = LoRa.parsePacket();` memeriksa apakah ada paket yang diterima melalui modul LoRa. Jika ada, maka ukuran paket akan disimpan dalam variabel `packetSize`.
- `if (packetSize) {` memeriksa apakah ada paket yang diterima. Jika ya, maka program akan melanjutkan ke bagian ini.
- `customSerial.print("Received packet ");` mencetak pesan bahwa paket telah diterima ke serial monitor.
- `while (LoRa.available()) {` memeriksa apakah ada data yang tersedia untuk dibaca dari modul LoRa. Jika ya, maka program akan melanjutkan ke bagian ini.
- `LoRaData = LoRa.readString();` membaca data yang diterima dari modul LoRa dan menyimpannya ke dalam variabel `LoRaData`. Variabel ini bertipe `String` yang merupakan tipe data string di Arduino.
- `LoRa.readString()` adalah fungsi yang membaca data dari modul LoRa hingga karakter newline (`\n`) dan mengembalikan hasilnya sebagai string.

### 4.4 Menerima Pesan ACK
```cpp
      if (LoRaData.startsWith("ACK#")) {
        customSerial.print("ACK Message Received : ");
        customSerial.println(LoRaData);
        // Remove "ACK," from the beginning of the string
        LoRaData.remove(0, 4);
```
- `if (LoRaData.startsWith("ACK#")) {` memeriksa apakah data yang diterima dimulai dengan "ACK#". Jika ya, maka program akan melanjutkan ke bagian ini.
- `customSerial.print("ACK Message Received : ");` mencetak pesan bahwa pesan ACK telah diterima ke serial monitor.
- `customSerial.println(LoRaData);` mencetak data ACK yang diterima ke serial monitor.
- `LoRaData.remove(0, 4);` menghapus "ACK," dari awal string `LoRaData`. Ini dilakukan untuk memisahkan ID dari data ACK yang diterima.

### 4.5 Memproses Data ACK
```cpp
        // Split the remaining string by commas
        int delimiterPos;
        ack_status = false;
        while ((delimiterPos = LoRaData.indexOf('#')) != -1) {
          String valueStr = LoRaData.substring(0, delimiterPos);
          int value = valueStr.toInt();
          LoRaData.remove(0, delimiterPos + 1);
```
- `int delimiterPos;` mendeklarasikan variabel `delimiterPos` yang akan digunakan untuk menyimpan posisi pemisah dalam string. Delimiter adalah karakter '#' yang digunakan untuk memisahkan ID dari data ACK yang diterima. Contoh: "ACK#123#456#789#" yang berarti ID 123, 456, dan 789.
- `ack_status = false;` mengatur status ACK menjadi false. Ini digunakan untuk menandakan bahwa ACK belum diterima.
- `while ((delimiterPos = LoRaData.indexOf('#')) != -1) {` memeriksa apakah ada karakter '#' dalam string `LoRaData`. Jika ada, maka program akan melanjutkan ke bagian ini.
   - `delimiterPos = LoRaData.indexOf('#');` mencari posisi karakter '#' dalam string `LoRaData` dan menyimpannya dalam variabel `delimiterPos`. Jika tidak ditemukan, maka nilai `delimiterPos` akan menjadi -1. -1 adalah nilai yang menunjukkan bahwa karakter '#' tidak ditemukan dalam string.
   - `delimiterPos` adalah variabel yang menyimpan posisi karakter '#' dalam string `LoRaData`. Ini digunakan untuk memisahkan ID dari data ACK yang diterima.
   - `indexOf('#')` adalah fungsi yang mencari posisi karakter '#' dalam string `LoRaData`. Jika ditemukan, maka fungsi ini akan mengembalikan posisi karakter tersebut. Jika tidak ditemukan, maka fungsi ini akan mengembalikan -1.
   `!= -1` adalah kondisi yang memeriksa apakah karakter '#' ditemukan dalam string `LoRaData`. Jika ditemukan, maka program akan melanjutkan ke bagian ini.
- `String valueStr = LoRaData.substring(0, delimiterPos);` mengambil substring dari `LoRaData` dari awal hingga posisi pemisah '#' dan menyimpannya dalam variabel `valueStr`.
  - `substring(0, delimiterPos)` adalah fungsi yang mengambil substring dari string `LoRaData` dari indeks 0 hingga indeks `delimiterPos`. Ini digunakan untuk mendapatkan ID dari data ACK yang diterima. Contohnya "ACK#123#456#789#" akan menghasilkan substring "123" dengan delimiterPos 3 karena '#' berada di indeks 3. Berarti ambil string yang didepan delimiterPos.
  - `valueStr` adalah variabel yang menyimpan substring dari string `LoRaData`. Ini digunakan untuk mendapatkan ID dari data ACK yang diterima.

- `int value = valueStr.toInt();` mengonversi substring `valueStr` menjadi integer dan menyimpannya dalam variabel `value`. Ini digunakan untuk mendapatkan ID dari data ACK yang diterima.
   - `toInt()` adalah fungsi yang mengonversi string menjadi integer. Ini digunakan untuk mengonversi substring `valueStr` menjadi integer.
   - `value` adalah variabel yang menyimpan ID dari data ACK yang diterima. Ini digunakan untuk memeriksa apakah ID yang diterima sesuai dengan ID perangkat.
   - `valueStr` adalah variabel yang menyimpan substring dari string `LoRaData`. Ini digunakan untuk mendapatkan ID dari data ACK yang diterima.
- `LoRaData.remove(0, delimiterPos + 1);` menghapus substring yang telah diproses dari `LoRaData`. Ini dilakukan untuk memisahkan ID dari data ACK yang diterima.
   - `remove(0, delimiterPos + 1)` adalah fungsi yang menghapus substring dari string `LoRaData` dari indeks 0 hingga indeks `delimiterPos + 1`. Ini digunakan untuk menghapus ID yang telah diproses dari string `LoRaData`. Contohnya "ACK#123#456#789#" akan menghasilkan string "456#789#" setelah pemrosesan ID 123.
   - `LoRaData` adalah variabel yang menyimpan data ACK yang diterima. Ini digunakan untuk memproses ID dari data ACK yang diterima.
   - `delimiterPos + 1` adalah posisi karakter '#' berikutnya setelah ID yang telah diproses. Ini digunakan untuk menghapus substring yang telah diproses dari string `LoRaData`.

#### 4.5.1 While
While adalah pernyataan yang digunakan untuk mengulangi blok kode selama kondisi tertentu terpenuhi. Contoh sintaksisnya adalah sebagai berikut:
```cpp
while (kondisi) {
  // blok kode yang akan diulang
} 
```

### 4.6 Memeriksa ID
```cpp
          if (value == (int)DEV_ID) {
            customSerial.println("DEV_ID is found in the ACK message. Send Data Success!");
            ack_status = true;
            
            display.fillRect(0, 50, 128, 40, SSD1306_BLACK);
            display.setCursor(0, 55);
            display.print("Send data : -OK-");
            display.display();
          }
        }
```
- `if (value == (int)DEV_ID) {` memeriksa apakah ID yang diterima sama dengan ID perangkat. Jika ya, maka program akan melanjutkan ke bagian ini.
- `customSerial.println("DEV_ID is found in the ACK message. Send Data Success!");` mencetak pesan bahwa ID perangkat ditemukan dalam pesan ACK ke serial monitor.
- `ack_status = true;` mengatur status ACK menjadi true. Ini digunakan untuk menandakan bahwa ACK telah diterima.
- `display.fillRect(0, 50, 128, 40, SSD1306_BLACK);` menghapus tampilan OLED pada area tertentu untuk memperbarui informasi.
- `display.setCursor(0, 55);` mengatur posisi kursor tampilan ke koordinat (0,55).
- `display.print("Send data : -OK-");` menampilkan pesan "Send data : -OK-" di layar OLED.
- `display.display();` memperbarui tampilan OLED untuk menampilkan pesan yang telah ditulis.

### 4.7 Memeriksa Status ACK
```cpp
        if(ack_status == false){
          customSerial.print("ACK data not found! ");
          if(waiting == false){
            int generateDelay = random(1000, 15000);
            randomDelay = currentMillis + generateDelay;
            waiting = true;
            customSerial.println("Resend data in " + String(generateDelay) + "s");
          }
        }
      }
    }
```
- `if(ack_status == false){` memeriksa apakah status ACK adalah false. Jika ya, maka program akan melanjutkan ke bagian ini.
- `customSerial.print("ACK data not found! ");` mencetak pesan bahwa data ACK tidak ditemukan ke serial monitor.
- `if(waiting == false){` memeriksa apakah status waiting adalah false. Jika ya, maka program akan melanjutkan ke bagian ini.
- `int generateDelay = random(1000, 15000);` menghasilkan angka acak antara 1 detik hingga 15 detik untuk menunggu sebelum mengirim ulang data.
- `randomDelay = currentMillis + generateDelay;` menyimpan waktu tunggu dalam variabel `randomDelay`. Ini digunakan untuk menghitung waktu tunggu sebelum mengirim ulang data.
- `waiting = true;` mengatur status waiting menjadi true. Ini digunakan untuk menandakan bahwa perangkat sedang menunggu sebelum mengirim ulang data.
- `customSerial.println("Resend data in " + String(generateDelay) + "s");` mencetak pesan bahwa perangkat akan mengirim ulang data setelah waktu tunggu yang telah ditentukan ke serial monitor.
- `generateDelay` adalah variabel yang menyimpan waktu tunggu sebelum mengirim ulang data. Ini digunakan untuk menghitung waktu tunggu sebelum mengirim ulang data.
- `randomDelay` adalah variabel yang menyimpan waktu tunggu dalam milidetik. Ini digunakan untuk menghitung waktu tunggu sebelum mengirim ulang data.
- `waiting` adalah variabel yang menyimpan status waiting. Ini digunakan untuk menandakan apakah perangkat sedang menunggu sebelum mengirim ulang data.
- `ack_status` adalah variabel yang menyimpan status ACK. Ini digunakan untuk menandakan apakah ACK telah diterima atau tidak.
- 

```cpp
    if(ack_status == false){
      if(waiting){
        display.fillRect(0, 55, 128, 40, SSD1306_BLACK);
        display.setCursor(0, 55);
        display.println("Waiting " + String(randomDelay-currentMillis) + " ms");
        display.display();
        if(currentMillis >= randomDelay){
          customSerial.print("Waiting time over, sending data..");
          appendFile(SD, "/log_transmitter.txt", dataLogJson);
          LoRa.beginPacket();
          LoRa.print(dataSendJson);
          LoRa.endPacket();
          customSerial.print(dataLogJson);
          
          display.fillRect(0, 50, 128, 40, SSD1306_BLACK);
          display.display();
          display.setCursor(0, 55);
          display.println("Sending data..");
          display.display();
          
          waiting = false;
        }
      }
    }
  }
  
  if((mode == 1)||(mode == 2)){
    int packetSize = LoRa.parsePacket();
    if (packetSize) {
      //received a packet
      customSerial.print("Received packet ");
      //read packet
      while (LoRa.available()) {
        LoRaData = LoRa.readString();
      }
      customSerial.println(LoRaData);
      DynamicJsonDocument doc(1024);
      DeserializationError error = deserializeJson(doc, LoRaData);
      if (error) {
        customSerial.print("ERROR : Invalid Data - ");
        customSerial.println(error.c_str());
      } else {
        SensorData received_data;
        received_data.id = doc["ID"];
        received_data.iradian = doc["Ir"];
        received_data.wspeed = doc["WS"];
        received_data.wdirect = doc["WD"];
        received_data.temp = doc["Temp"];
        received_data.hum = doc["Hum"];
        received_data.atmp = doc["AP"];
        received_data.rain = doc["Rain"];
        
        String result = "ID : " + String(received_data.id) + "\n" + 
                 "Iradian : " + String(received_data.iradian) + "\n" + 
                 "WindSpeed : " + String(received_data.wspeed) + "\n" + 
                 "WindDirection : " + String(received_data.wdirect) + "\n" + 
                 "Temperature : " + String(received_data.temp) + "\n" + 
                 "Humidity : " + String(received_data.hum) + "\n" + 
                 "AtmosphericPressure : " + String(received_data.atmp) + "\n" + 
                 "Rainfall : " + String(received_data.rain);
        customSerial.println(result);
        bool is_collected = false;
        // Output the data to the customSerial port
        for (const SensorData& entry : node_data) {
          if(received_data.id == entry.id){
            is_collected = true;
            customSerial.println("Data From This Node is Already Collected");
          }
          customSerial.print("ID: ");
          customSerial.println(entry.id);
          display.fillRect(0, 45, 128, 10, SSD1306_BLACK);
          display.setCursor(0, 45);
          display.println("Received from " + String(entry.id));
          display.display();
        }
        if(!is_collected){
          // is it new data?
          node_data.push_back(received_data);
        }
      }
    }
    if (currentMillis - previousAckMillisRead >= DELAY_ACK) {
      previousAckMillisRead = currentMillis;
      ack_message = "ACK#";
      display.fillRect(0, 15, 128, 50, SSD1306_BLACK);
      display.setCursor(0, 15);
      display.print("Received(ID): ");
      for (const SensorData &entry : node_data)
      {
        ack_message += String(entry.id) + "#";
        display.print(String(entry.id) + ", ");
      }
      display.display();
      
      LoRa.beginPacket();
      LoRa.print(ack_message);
      LoRa.endPacket();
      delay(100);
      LoRa.beginPacket();
      LoRa.print(ack_message);
      LoRa.endPacket();
      delay(100);
      LoRa.beginPacket();
      LoRa.print(ack_message);
      LoRa.endPacket();
      
      customSerial.print("Sending ACK message : ");
      customSerial.println(ack_message);

      display.fillRect(50, 55, 128, 20, SSD1306_BLACK);
      display.setCursor(50, 55);
      display.println("ACK Sent");
      display.display();
    }
    if (currentMillis - previousPublishMillisRead >= INTERVAL_MQTT) {
      previousPublishMillisRead = currentMillis;
      // save log to sdcard
      saveLog();
      // send data with mqtt protocol
      sendData();
      // clear data
      node_data.clear();
    }
    
    int remaining_s = ((INTERVAL_MQTT - (currentMillis - previousPublishMillisRead))/1000);
    int remaining_m = remaining_s/60;
    remaining_s = remaining_s - (remaining_m*60);
    display.fillRect(0, 55, 128, 20, SSD1306_BLACK);
    display.setCursor(0, 55);
    display.println(String(remaining_m) + "m" + String(remaining_s) + "s");
    display.setCursor(55, 55);
    display.println("ACK in " + String((DELAY_ACK - (currentMillis - previousAckMillisRead))/1000) + "s");
    display.display();
    
  }
}
