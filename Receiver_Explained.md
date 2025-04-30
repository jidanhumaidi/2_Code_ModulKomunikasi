#include "ArduinoJson.h"
#include <vector>
#include "time.h"
#include "stdbool.h"

char TimeNow[30];
const char *ntpServer = "pool.ntp.org";
const long gmtOffset_sec = 21600;
const int daylightOffset_sec = 3600;

#include <PubSubClient.h>
#define mqtt_server "192.168.220.122"
#define mqtt_port 1883
#define mqtt_username ""
#define mqtt_password ""

String client_id = "NODE-" + String(DEV_ID);
String TOPIC_IR = "PLN_NP_Testing_Iradian_";
String TOPIC_WS = "PLN_NP_Testing_WindSpeed_";
String TOPIC_WD = "PLN_NP_Testing_WindDirection_";
String TOPIC_TEMP = "PLN_NP_Testing_Temperature_";
String TOPIC_HUM = "PLN_NP_Testing_Humidity_";
String TOPIC_ATMP = "PLN_NP_Testing_AtmosphericPressure_";
String TOPIC_RAIN = "PLN_NP_Testing_RainFall_";

WiFiClient wifiClient;

EthernetClient ethClient;
byte mac[] = { 0xDE, 0xED, 0xBA, 0xFE, 0xFE, 0xEF };

PubSubClient client;

String LoRaData = "";

// set time delay to send ack message
#define DELAY_ACK 20000
// set interval to send data via MQTT Protocol
// #define INTERVAL_MQTT 300000
#define INTERVAL_MQTT 100000 //nyoba aja biar cepett

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

std::vector<SensorData> node_data;
String ack_message = "";

void reconnect()
{
    int bcount = 0;
    // Loop until we're reconnected
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
                { // Dynamic IP setup
                    customSerial.println("DHCP OK!");
                }
                else
                {
                    customSerial.println("Failed to configure Ethernet using DHCP");
                    // Check for Ethernet hardware present
                    if (Ethernet.hardwareStatus() == EthernetNoHardware)
                    {
                        customSerial.println("Ethernet shield was not found.  Sorry, can't run without hardware. :(");
                        while (true)
                        {
                            delay(500); // do nothing, no point running without Ethernet hardware
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

// save log to SDCard
// void saveLog()
// {
//     for (const SensorData &entry : node_data)
//     {
//         LocalTime();
//         char tmp[10];
//         if (entry.temp < 0)
//             sprintf(tmp, "-%d", (int)entry.temp);
//         else
//             sprintf(tmp, "%d", (int)entry.temp);

//         sprintf(dataLogJson, "{"
//                              "\"ID\": %d, "
//                              "\"Timestamp\": \"%s\", "
//                              "\"Iradian\": %d, "
//                              "\"WindSpeed\": %d.%02d, "
//                              "\"WindDirection\": %d.%02d, "
//                              "\"Temperature\": %s.%02d, "
//                              "\"RelativeHumidity\": %d.%02d, "
//                              "\"AtmosphericPressure\": %d, "
//                              "\"Rainfall\": %d.%02d"
//                              "},\n",
//                 entry.id,
//                 TimeNow,
//                 entry.iradian,
//                 (int)entry.wspeed, abs((int)(entry.wspeed * 100) % 100),
//                 (int)entry.wdirect, abs((int)(entry.wdirect * 100) % 100),
//                 tmp, abs((int)(entry.temp * 100) % 100),
//                 (int)entry.hum, abs((int)(entry.hum * 100) % 100),
//                 entry.atmp,
//                 (int)entry.rain, abs((int)(entry.rain * 100) % 100));
//         customSerial.println("Saving data from ID-" + String(entry.id) + "...");
//         appendFile(SD, "/log_receiver.txt", dataLogJson);
//     }
// }

// send data using MQTT Protocol
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
