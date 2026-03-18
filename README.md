# ESP8266-BME280-ESPHome
First ESPHome project

## 2026-03-18 Motivation

I have an ESP8266 that connects to a BME280 sensor to measure and publish temperature, humidity and pressure via MQTT. One previous project <https://github.com/HankB/ESP8266_RTOS_BME280> no longer builds. Another using PlatformIO and based on the Arduino SDK <https://github.com/HankB/esp8266_MQTT_BME280> builds but has weird problems. Depending on the amount of serial I/O the project does, it either doesn;t run at all, runs but produces wildly wrong readings or produces reasonable readings for a bit before reverting to the wild readings. IAC, I've been meaning to take a look at ESPHome and that seems particularly suitable because I'll be integrating the readings with HomeAssistant.

## 2026-03-18 Plan

Explore, get something working. Add MQTT publishing and then test, test and test.
