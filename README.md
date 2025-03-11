
>👩🏻‍💻Feby 🗓 11 March 2025

# MICROCONTROLLER BASED ESP32 TEMPERATURE, HUMIDITY & LIGHT INTENSITY INDICATOR 
---
## **A. Introduction**

This project is a temperature check activity using Wokwi based on ESP32 Microcontroller and Visual Studio Code C++ programming language. The output of this simulation is display temperature  temperature, humidity & light intensity indicator.

## **B. Diagram**
This diagram was created using the ESP32 Microcontroller based Wokwi simulation.

<img width="1020" alt="1" src="https://github.com/user-attachments/assets/c86ca5cd-7f04-44e6-ab0b-ffceb9dee5a7" />




```json
{
  "version": 1,
  "author": "Anonymous maker",
  "editor": "wokwi",
  "parts": [
    {
      "type": "board-esp32-devkit-c-v4",
      "id": "esp",
      "top": 28.8,
      "left": -110.36,
      "rotate": 9000,
      "attrs": {}
    },
    { "type": "wokwi-dht22", "id": "dht1", "top": 557.1, "left": 90.6, "attrs": {} },
    {
      "type": "wokwi-photoresistor-sensor",
      "id": "ldr1",
      "top": 300.8,
      "left": -277.6,
      "attrs": {}
    },
    {
      "type": "board-ssd1306",
      "id": "oled1",
      "top": 406.34,
      "left": -38.17,
      "attrs": { "i2cAddress": "0x3c" }
    }
  ],
  "connections": [
    [ "esp:TX", "$serialMonitor:RX", "", [] ],
    [ "esp:RX", "$serialMonitor:TX", "", [] ],
    [ "dht1:DATA", "esp:19", "green", [ "v0" ] ],
    [ "oled1:SDA", "esp:21", "black", [ "v0" ] ],
    [ "oled1:SCL", "esp:22", "black", [ "v0" ] ],
    [ "oled1:GND", "esp:GND.2", "black", [ "v0" ] ],
    [ "oled1:VCC", "esp:3V3", "black", [ "v105.6", "h-105.45" ] ],
    [ "dht1:SDA", "esp:19", "black", [ "v0" ] ],
    [ "dht1:VCC", "esp:3V3", "black", [ "v0", "h-259.2" ] ],
    [ "ldr1:AO", "esp:34", "black", [ "h-19.2", "v-0.7" ] ],
    [ "ldr1:VCC", "esp:3V3", "black", [ "h-9.6", "v96" ] ],
    [ "ldr1:GND", "dht1:NC", "black", [ "h9.6", "v335.6", "h259.2" ] ]
  ],
  "dependencies": {}
}
```

## **C. Program Code**
This program code is to set the traffic lights to turn on alternately according to a predetermined duration.

```cpp
#include <Arduino.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <DHT.h>

#define DHTPIN 19
#define DHTTYPE DHT22
#define TIMEDHT 1000

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64

#define LDR_PIN 34 


float humidity, celsius;
uint32_t timerDHT = TIMEDHT;
DHT dht(DHTPIN, DHTTYPE);


Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);


void getTemperature() {
  if ((millis() - timerDHT) > TIMEDHT) {
    timerDHT = millis();
    humidity = dht.readHumidity();
    celsius = dht.readTemperature();

    if (isnan(humidity) || isnan(celsius)) {
      Serial.println("Gagal membaca sensor DHT!");
      return;
    }
  }
}

void setup() {
  Serial.begin(115200);
  dht.begin();
  
  if (!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println("OLED gagal diinisialisasi");
    for (;;);
  }
  
  display.clearDisplay();
}

void loop() {
  getTemperature();
  int lightValue = analogRead(LDR_PIN); 
  
  Serial.print("Temp: ");
  Serial.print(celsius);
  Serial.print(" C, Humidity: ");
  Serial.print(humidity);
  Serial.print("%, Light: ");
  Serial.println(lightValue);

  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(WHITE);
  
  display.setCursor(0, 0);
  display.print("Temp: ");
  display.print(celsius);
  display.println(" C");

  display.setCursor(0, 16);
  display.print("Humidity: ");
  display.print(humidity);
  display.println(" %");

  display.setCursor(0, 32);
  display.print("Light: ");
  display.print(lightValue);

  display.display();
  delay(1000);
}
```
For more details, please follow the implementation steps in the temperature check report (pdf).
