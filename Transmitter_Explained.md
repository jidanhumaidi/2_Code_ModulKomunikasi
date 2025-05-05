# Transmitter
## 1. Include Libraries
```cpp
#include <ModbusMaster.h>
```
`ModbusMaster.h` adalah library yang digunakan untuk komunikasi Modbus RTU. Contoh penggunaan dapat dilihat pada [ModbusMaster](https://github.com/4-20ma/ModbusMaster). 

## 2. Define Constants and Variables
```cpp
#define slaveID 0xFF
```
- `slaveID` adalah ID dari slave yang akan digunakan untuk komunikasi Modbus RTU. Pada contoh ini, slaveID diatur menjadi 0xFF.
- `0xFF` adalah ID default dari slave yang digunakan pada alat ukur. 

## 3. Define Modbus Register Address
```cpp
#define TEMPERATURE_REGISTER 0x0009
```
`0x0009` adalah alamat register untuk baca data alat ukur.

## 4. Define Variables 
```cpp
ModbusMaster node;
```
`ModbusMaster node` adalah deklarasi sebuah objek bernama node dari kelas ModbusMaster yang berasal dari library ModbusMaster.h

## 5. Define Variables for Data Storage
```cpp
char dataSendJson[512];
char dataLogJson[512];
```
`dataSendJson` dan `dataLogJson` adalah array karakter yang digunakan untuk menyimpan data dalam format JSON. Ukuran array diatur menjadi 512 karakter.

## 6. Define Variables for Sensor Data
```cpp
bool ack_status = true;
unsigned long randomDelay = 0;
bool waiting = false;
```
- `ack_status` adalah variabel boolean yang digunakan untuk menandakan status ACK dari server. act_status diberi nilai true berarti kondisi awal dari ACK adalah true. Dan true berarti ACK sudah diterima.
- `randomDelay` adalah variabel untuk menyimpan delay acak yang digunakan untuk menghindari tabrakan data saat pengiriman.
- `waiting` adalah variabel boolean yang digunakan untuk menandakan apakah sistem sedang menunggu data dari server.

## 7. Modbus RTU Function
```cpp
void modbusRTU()
{
    int buffersize = 7;
    float data[buffersize];
    uint8_t i;
    uint8_t j;
    uint8_t result;

    result = node.readHoldingRegisters(TEMPERATURE_REGISTER, 12);

    if (result == node.ku8MBSuccess)
    {
        temp = (node.getResponseBuffer(0) / 100) - 40; // Temperature in °C
        hum = node.getResponseBuffer(1) / 100.0; // Humidity in percentage
        atmp = (int)node.getResponseBuffer(2) / 10.0; // Atmospheric pressure in hPa
        wspeed = node.getResponseBuffer(3) / 100.0; // Wind speed in m/s
        wdirect = node.getResponseBuffer(4) / 10.0; // Wind direction in degrees
        rain = node.getResponseBuffer(5) / 10.0; // Rainfall in mm
        iradian = (int)node.getResponseBuffer(6); // Radiation in W/m^2

        customSerial.println();
        customSerial.print("Temperature : ");
        customSerial.println(temp);
        customSerial.print("Humadity : ");
        customSerial.println(hum);
        customSerial.print("Atmosphere : ");
        customSerial.println(atmp);
        customSerial.print("Wind Speed : ");
        customSerial.println(wspeed);
        customSerial.print("Wind Direction : ");
        customSerial.println(wdirect);
        customSerial.print("Rainfall : ");
        customSerial.println(rain);
        customSerial.print("Iradian : ");
        customSerial.println(iradian);
    }
    else {
        customSerial.println("Get data sensor failed!");
        customSerial.print("Result : ");
        customSerial.println(result);
    }
}
```
### 7.1 Buffersize 
```cpp
int buffersize = 7;
```
`buffer` adalah area penyimpanan sementara di memori yang digunakan untuk menyimpan data yang sedang diproses atau ditransfer. Dalam konteks ini, buffer digunakan untuk menyimpan data yang diterima dari alat ukur sebelum data tersebut diolah atau ditampilkan. Buffer membantu memastikan bahwa data dapat ditangani dengan efisien, terutama ketika data diterima dalam jumlah besar atau secara terus-menerus.

`buffersize` adalah ukuran buffer yang digunakan untuk menyimpan data dari alat ukur. Pada contoh ini, ukuran buffer diatur menjadi 7.

### 7.1 Buffersize 
```cpp
int buffersize = 7;
```
Buffer adalah tempat penyimpanan sementara untuk data yang sedang diproses. Dalam hal ini, `buffersize` menentukan ukuran buffer, yaitu 7.

Bayangkan buffer seperti kotak dengan 7 sekat, di mana setiap sekat dapat menyimpan satu item data. Buffer ini memiliki kapasitas terbatas, sehingga hanya dapat menampung hingga 7 item. Jika data baru masuk saat buffer penuh, salah satu dari dua hal berikut dapat terjadi:
- Data baru tidak akan masuk (buffer penuh).
- Data lama akan digantikan oleh data baru (buffer melingkar).

**Contoh Penggunaan:**
Buffer ini dapat digunakan untuk menyimpan nilai suhu dari sensor, seperti:
```
[23.5, 24.0, 24.2, 24.5, 24.7, 24.9, 25.0]
```

### 7.2 Data Array
```cpp
float data[buffersize];
```
Array `data` adalah struktur penyimpanan yang dapat menampung 7 angka desimal (float). Bayangkan seperti kotak dengan 7 slot bernomor 0-6, dimana setiap slot dapat menyimpan satu angka desimal.

**Contoh penggunaan:**
```cpp
data[0] = 25.3;  // Slot 1
data[1] = 25.5;  // Slot 2
data[2] = 25.4;  // Slot 3
```

Array ini biasa digunakan untuk:
- Menyimpan pembacaan sensor secara berurutan
- Mengumpulkan data untuk perhitungan rata-rata
- Menyimpan data sementara sebelum diproses lebih lanjut

### 7.3 Read Holding Registers
```cpp
uint8_t i;
uint8_t j;
uint8_t result;
```
Mari saya jelaskan apa arti kode-kode ini:

```
uint8_t i;
uint8_t j;
uint8_t result;
```

Kode ini mendefinisikan tiga variabel bernama `i`, `j`, dan `result`. Mari kita bahas:

1. **`uint8_t` adalah tipe data**: 
   - `uint` = unsigned integer (bilangan bulat tanpa tanda negatif)
   - `8` = 8 bit (1 byte)
   - `t` = singkatan dari "type"
   
   Jadi `uint8_t` adalah tipe data untuk bilangan bulat positif yang bisa menyimpan nilai dari 0 sampai 255.

2. **Seperti Kotak Kecil**: Bayangkan Anda memiliki tiga kotak kecil yang masing-masing hanya bisa menampung angka 0-255:
   - Kotak pertama dinamai `i`
   - Kotak kedua dinamai `j`
   - Kotak ketiga dinamai `result`

3. **Kegunaannya**: Variabel-variabel ini kemungkinan digunakan untuk:
   - `i` dan `j` biasanya untuk penghitung (counter) dalam loop/perulangan
   - `result` untuk menyimpan hasil perhitungan atau operasi

4. **Contoh Penggunaan**:
   ```cpp
   i = 0; // Mulai dari 0
   while (i < bufferSize) {  // Lakukan perulangan 7 kali
     data[i] = 25.0;         // Isi setiap posisi dengan nilai 25.0
     i = i + 1;             // Tambah i dengan 1
   }
   ```

5. **Mengapa menggunakan `uint8_t`?**: 
   - Pada ESP32, memori terbatas
   - `uint8_t` menggunakan ruang memori yang kecil (hanya 1 byte)
   - Untuk penghitung yang kecil (0-255), ini lebih efisien daripada tipe data yang lebih besar

Jadi, ketiga baris kode tersebut hanya mempersiapkan tiga "wadah" kecil bernama `i`, `j`, dan `result` yang masing-masing bisa menyimpan angka bulat dari 0 sampai 255, kemungkinan untuk digunakan sebagai penghitung atau untuk menyimpan hasil perhitungan sederhana.

### 7.4 Read Holding Registers
```cpp
result = node.readHoldingRegisters(TEMPERATURE_REGISTER, 12);
```
Ini adalah kode yang melakukan pembacaan data dari sebuah perangkat. Mari saya jelaskan:

`result = node.readHoldingRegisters(TEMPERATURE_REGISTER, 12);`

Kode ini melakukan pembacaan data suhu dan menyimpan hasilnya ke dalam variable `result`. Lebih detailnya:

1. **Apa yang terjadi**: 
   - ESP32 sedang berkomunikasi dengan perangkat lain (seperti sensor atau peralatan industri)
   - ESP32 meminta data dari perangkat tersebut
   - Hasil pembacaan disimpan dalam variabel `result`

2. **Komponen kode**:
   - `node` adalah objek yang mewakili perangkat yang dihubungkan
   - `readHoldingRegisters` adalah fungsi untuk membaca data dari perangkat
   - `TEMPERATURE_REGISTER` adalah alamat (lokasi) di mana data suhu disimpan pada perangkat
   - `12` adalah jumlah register/nilai yang ingin dibaca
   - `result` akan bernilai kode status (biasanya 0 berarti sukses, nilai lain berarti error)

3. **Dalam bahasa sehari-hari**:
   "Tolong ambilkan 12 data suhu dari perangkat, mulai dari posisi TEMPERATURE_REGISTER, dan beritahu saya apakah pengambilan berhasil atau tidak dengan menyimpan statusnya di result."

4. **Protokol Modbus**: 
   Kode ini kemungkinan menggunakan protokol Modbus, yang umum digunakan dalam perangkat industri dan automasi. "Holding Registers" adalah salah satu jenis memori dalam protokol Modbus.

5. **Nilai `result`**:
   - Jika `result = 0`: pembacaan berhasil
   - Jika `result = nilai lain`: ada masalah dalam pembacaan

Jadi, kode ini pada dasarnya adalah perintah ESP32 untuk mengambil sekelompok data suhu dari perangkat lain, kemudian mencatat apakah pengambilan data berhasil atau tidak dalam variabel `result`.

### 7.5 Check Result
```cpp
if (result == node.ku8MBSuccess)
    {}
```
`result == node.ku8MBSuccess` adalah ekspresi perbandingan yang memeriksa apakah pembacaan data yang sebelumnya dilakukan berhasil atau tidak.

Mari saya jelaskan lebih detail:

1. **Apa yang dibandingkan**:
   - `result` adalah nilai yang disimpan dari operasi `readHoldingRegisters` sebelumnya
   - `node.ku8MBSuccess` adalah konstanta yang mendefinisikan kode sukses dalam komunikasi Modbus

2. **Tanda `==`** artinya "sama dengan" - ini membandingkan dua nilai dan menghasilkan:
   - `true` (benar) jika kedua nilai sama
   - `false` (salah) jika kedua nilai berbeda

3. **Dalam bahasa sehari-hari**:
   "Apakah hasil pembacaan data kita berhasil?"

4. **Contoh penggunaan dalam kode**:
   ```cpp
   if (result == node.ku8MBSuccess) {
     // Kode yang dijalankan jika pembacaan berhasil
     // Misalnya, memproses data suhu yang dibaca
   } else {
     // Kode yang dijalankan jika pembacaan gagal
     // Misalnya, menampilkan pesan error
   }
   ```

5. **Tentang `ku8MBSuccess`**:
   - `k` biasanya berarti konstanta
   - `u8` berarti unsigned 8-bit (seperti uint8_t)
   - `MB` mungkin singkatan dari Modbus
   - `Success` artinya sukses/berhasil
   
   Jadi `ku8MBSuccess` adalah konstanta yang menandakan "komunikasi Modbus berhasil", biasanya bernilai 0.

Jadi keseluruhan ekspresi `result == node.ku8MBSuccess` memeriksa apakah pembacaan data dari perangkat berhasil dilakukan tanpa error. Ekspresi ini biasanya digunakan dalam pernyataan kondisional (if) untuk menentukan langkah selanjutnya dalam program.

### 7.6 Get Response Buffer
```cpp
temp = (node.getResponseBuffer(0) / 100) - 40; // Temperature in °C
hum = node.getResponseBuffer(1) / 100.0; // Humidity in percentage
atmp = (int)node.getResponseBuffer(2) / 10.0; // Atmospheric pressure in hPa
wspeed = node.getResponseBuffer(3) / 100.0; // Wind speed in m/s
wdirect = node.getResponseBuffer(4) / 10.0; // Wind direction in degrees
rain = node.getResponseBuffer(5) / 10.0; // Rainfall in mm
iradian = (int)node.getResponseBuffer(6); // Radiation in W/m^2
```
Kode yang Anda tunjukkan adalah proses pengambilan data dari berbagai sensor cuaca setelah berhasil membaca data dari perangkat. Mari saya jelaskan satu per satu:

```cpp
temp = (node.getResponseBuffer(0) / 100) - 40;      // Temperature in °C
hum = node.getResponseBuffer(1) / 100.0;           // Humidity in percentage
atmp = (int)node.getResponseBuffer(2) / 10.0;      // Atmospheric pressure in hPa
wspeed = node.getResponseBuffer(3) / 100.0;        // Wind speed in m/s
wdirect = node.getResponseBuffer(4) / 10.0;        // Wind direction in degrees
rain = node.getResponseBuffer(5) / 10.0;           // Rainfall in mm
iradian = (int)node.getResponseBuffer(6);          // Radiation in W/m^2
```

Mari saya jelaskan secara sederhana:

1. **Mengambil data mentah**:
   - `node.getResponseBuffer(posisi)` mengambil data mentah dari buffer (tempat penyimpanan) yang telah diisi oleh perintah `readHoldingRegisters` sebelumnya.
   - Angka dalam kurung (0, 1, 2, ...) adalah posisi data dalam buffer.

2. **Mengkonversi data mentah**:
   - Data mentah perlu dikonversi menjadi nilai yang bermakna dengan rumus tertentu.
   - Misalnya, data suhu membutuhkan pembagian dengan 100 dan dikurangi 40.

3. **Untuk setiap parameter cuaca**:

   - **Suhu (temp)**:
     - Ambil data dari posisi 0
     - Bagi dengan 100 lalu kurangi 40
     - Hasilnya dalam °C

   - **Kelembaban (hum)**:
     - Ambil data dari posisi 1
     - Bagi dengan 100
     - Hasilnya dalam persentase (%)

   - **Tekanan atmosfer (atmp)**:
     - Ambil data dari posisi 2
     - Ubah ke bilangan bulat dengan `(int)`
     - Bagi dengan 10
     - Hasilnya dalam hPa (hektopascal)

   - **Kecepatan angin (wspeed)**:
     - Ambil data dari posisi 3
     - Bagi dengan 100
     - Hasilnya dalam m/s (meter per detik)

   - **Arah angin (wdirect)**:
     - Ambil data dari posisi 4
     - Bagi dengan 10
     - Hasilnya dalam derajat (°)

   - **Curah hujan (rain)**:
     - Ambil data dari posisi 5
     - Bagi dengan 10
     - Hasilnya dalam mm (milimeter)

   - **Radiasi matahari (iradian)**:
     - Ambil data dari posisi 6
     - Tidak perlu dibagi (langsung diambil)
     - Hasilnya dalam W/m² (Watt per meter persegi)

Intinya, kode ini mengubah data mentah yang diterima dari sensor cuaca menjadi nilai yang lebih bermakna dan dalam satuan yang tepat untuk digunakan dalam aplikasi atau ditampilkan ke pengguna.

### 7.7 Print Data to Serial Monitor
```cpp
customSerial.println();
customSerial.print("Temperature : ");
customSerial.println(temp);
customSerial.print("Humadity : ");
customSerial.println(hum);
customSerial.print("Atmosphere : ");
customSerial.println(atmp);
customSerial.print("Wind Speed : ");
customSerial.println(wspeed);
customSerial.print("Wind Direction : ");
customSerial.println(wdirect);
customSerial.print("Rainfall : ");
customSerial.println(rain);
customSerial.print("Iradian : ");
customSerial.println(iradian);
```

Kesimpilannya, fungsi modbusRTU() bertugas untuk membaca data dari alat ukur menggunakan protokol Modbus RTU. Setelah berhasil membaca data, fungsi ini juga mencetak hasil pembacaan ke Serial Monitor untuk pemantauan.

## 8. Set Data Function
Fungsi `setData()` bertanggung jawab untuk mengambil data sensor dan menampilkannya dalam format JSON serta di layar OLED.
```cpp
void setData()
{
    modbusRTU();

    char tmp[10];

    if (temp < 0)
        sprintf(tmp, "-%d", (int)temp);
    else
        sprintf(tmp, "%d", (int)temp);

    sprintf(dataLogJson, "{"
                        "\"Iradian\": %d, "
                        "\"WindSpeed\": %d.%02d, "
                        "\"WindDirection\": %d.%02d, "
                        "\"Temperature\": %s.%02d, "
                        "\"RelativeHumidity\": %d.%02d, "
                        "\"AtmosphericPressure\": %d, "
                        "\"Rainfall\": %d.%02d"
                        "},\n",
            iradian, (int)wspeed, abs((int)(wspeed * 100) % 100), (int)wdirect, abs((int)(wdirect * 100) % 100), tmp, abs((int)(temp * 100) % 100), (int)hum, abs((int)(hum * 100) % 100), atmp, (int)rain, abs((int)(rain * 100) % 100));

    sprintf(dataSendJson, "{"
                        "\"ID\":%d,"
                        "\"Ir\":%d,"
                        "\"WS\":%d.%02d,"
                        "\"WD\":%d.%02d,"
                        "\"Temp\":%s.%02d,"
                        "\"Hum\":%d.%02d,"
                        "\"AP\":%d,"
                        "\"Rain\":%d.%02d"
                        "}",
            (int)DEV_ID, 
            iradian, 
            (int)wspeed, abs((int)(wspeed * 100) % 100), 
            (int)wdirect, abs((int)(wdirect * 100) % 100), 
            tmp, abs((int)(temp * 100) % 100), 
            (int)hum, abs((int)(hum * 100) % 100), 
            atmp, 
            (int)rain, abs((int)(rain * 100) % 100));

    display.fillRect(0, 10, 128, 40, SSD1306_BLACK);
    display.setCursor(0, 10);
    display.print("Tmp:" + String(tmp) + "." + String(abs((int)(temp * 100) % 100)) + "C");
    display.setCursor(0, 20);
    display.print("Hum:" + String((int)hum) + "." + String(abs((int)(hum * 100) % 100)));
    display.setCursor(0, 30);
    display.print("WD :" + String((int)wdirect) + "." + String(abs((int)(wdirect * 100) % 100)));
    display.setCursor(70, 10);
    display.print("WS:" + String((int)wspeed) + "." + String(abs((int)(wspeed * 100) % 100)));
    display.setCursor(70, 20);
    display.print("Ir:" + String(iradian));
    display.setCursor(70, 30);
    display.print("AP:" + String(atmp));
    display.setCursor(0, 40);
    display.print("Rain:" + String((int)rain) + "." + String(abs((int)(rain * 100) % 100)));
    
    display.display();
}
```
### 8.1 Getting Data
```cpp
void setData()
{
   modbusRTU();
   char tmp[10];
```
- `void setData()` - Mendefinisikan fungsi yang tidak mengembalikan nilai
- `modbusRTU()` - Memanggil fungsi untuk membaca data sensor
- `char tmp[10]` - Array karakter untuk menyimpan nilai suhu sementara

### 8.2 Temperature Formatting
```cpp
if (temp < 0)
   sprintf(tmp, "-%d", (int)temp);
else
   sprintf(tmp, "%d", (int)temp);
```
- Memeriksa apakah suhu negatif
- `sprintf()` memformat suhu ke string:
  - Jika negatif: menambah tanda minus
  - Jika positif: langsung nilai integer

### 8.3 Sprintf JSON Data Formatting
`sprintf()` adalah fungsi untuk memformat string, seperti membuat template dengan isian yang bisa diubah. Contoh:

```cpp
sprintf(buffer, "Suhu: %d.%02d", 25, 50);  // Hasilnya "Suhu: 25.50"
```

Komponen `sprintf()`:
- `buffer`: tempat menyimpan hasil string
- `"format"`: template dengan penanda khusus
- `nilai`: data yang akan dimasukkan ke template

Penanda format umum:
- `%d`: angka bulat
- `%s`: string
- `%f`: angka desimal
- `.02d`: angka dengan 2 digit

### 8.4 JSON Data Formatting
```cpp
sprintf(dataLogJson, "{...}");
sprintf(dataSendJson, "{...}");
```
- Membuat dua string JSON berbeda:
  - `dataLogJson` - Format lengkap untuk logging
  - `dataSendJson` - Format ringkas untuk pengiriman
- Menggunakan `sprintf()` untuk memformat nilai sensor ke string
- `abs()` digunakan untuk mendapatkan nilai absolut dari bagian desimal

### 8.5 Display Output
```cpp
display.fillRect(0, 10, 128, 40, SSD1306_BLACK);
display.setCursor(0, 10);
// ... display commands ...
display.display();
```
- Menghapus area tampilan dengan `fillRect()`
- Mengatur posisi cursor untuk setiap baris
- Menampilkan nilai sensor dengan format dan label yang sesuai
- `display.display()` memperbarui tampilan OLED
- Menambahkan fungsi untuk mengupdate data secara berkala

