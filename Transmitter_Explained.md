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
`buffersize` adalah ukuran buffer yang digunakan untuk menyimpan data dari alat ukur. Pada contoh ini, ukuran buffer diatur menjadi 7.

### 7.2 Data Array
```cpp
float data[buffersize];
```
`data[buffersize]` adalah array bertipe float yang digunakan untuk menyimpan data dari alat ukur. Ukuran array diatur sesuai dengan `buffersize` yang telah didefinisikan sebelumnya.

### 7.3 Read Holding Registers

```cpp
uint8_t i;
uint8_t j;
uint8_t result;
```

```cpp
result = node.readHoldingRegisters(TEMPERATURE_REGISTER, 12);
```
```cpp
if (result == node.ku8MBSuccess)
    {}
```


```cpp
temp = (node.getResponseBuffer(0) / 100) - 40; // Temperature in °C
hum = node.getResponseBuffer(1) / 100.0; // Humidity in percentage
atmp = (int)node.getResponseBuffer(2) / 10.0; // Atmospheric pressure in hPa
wspeed = node.getResponseBuffer(3) / 100.0; // Wind speed in m/s
wdirect = node.getResponseBuffer(4) / 10.0; // Wind direction in degrees
rain = node.getResponseBuffer(5) / 10.0; // Rainfall in mm
iradian = (int)node.getResponseBuffer(6); // Radiation in W/m^2
```


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








## 8. Set Data Function
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
