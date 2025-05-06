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

```cpp
void setup() {}
```

```cpp
  customSerial.begin(9600, SERIAL_8N1, 0, 1);
```

```cpp
  pinMode(SW1, INPUT);
  pinMode(SW2, INPUT);
  pinMode(SELECT_BUTTON, INPUT);
```

```cpp
  //initialize OLED
  Wire.begin(OLED_SDA, OLED_SCL);
  if(!display.begin(SSD1306_SWITCHCAPVCC, 0x3c, false, false)) { // Address 0x3C for 128x32
    customSerial.println(F("SSD1306 allocation failed"));
    for(;;); // Don't proceed, loop forever
  }
```

```cpp
  display.clearDisplay();
  display.setTextColor(WHITE);
  display.setTextSize(1);
  display.setCursor(0,0);
```

```cpp
  //SPI LoRa pins
  LoRa.setSPI(LoraSpi);
  LoraSpi.begin(SCK, MISO, MOSI, SS);
  //setup LoRa transceiver module
  LoRa.setPins(SS, RST, DIO0);
```

```cpp  
  if (!LoRa.begin(BAND)) {
    restartDevice("Starting LoRa Failed", 5);
    while (1);
  }
```

```cpp
  // initialize SD Card
  pinMode(SD_CS, OUTPUT);
  digitalWrite(SD_CS, HIGH);
```

```cpp
  // setup SDCard SPI
  SPI.begin(SD_SCK , SD_MISO, SD_MOSI);
  SPI.setClockDivider(SPI_CLOCK_DIV2); // Set the SPI clock speed
  delay(100);
```

```cpp
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
```

```cpp
  else {    
    display.clearDisplay();
    display.setCursor(0, 10);
    display.print("Skip Reading SDCard..");
    display.display();
    customSerial.println("Reading SDCard skipped");
  }
  ```

```cpp
  display.clearDisplay();
  display.setCursor(0,0);
  ```

```cpp
  node.begin(slaveID, customSerial);
  if(digitalRead(SW1)){
    mode = 0;
    customSerial.println("MODE TRANSMITTER");
    display.print("ID-" + String(DEV_ID));
    display.setCursor(60,0);
    display.println("TRANSMITTER");
    display.display();
  }
```

```cpp
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
```

```cpp
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
```

```cpp
  if(mode == 0)
    setData();
```

## 4. Fungsi Loop
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
