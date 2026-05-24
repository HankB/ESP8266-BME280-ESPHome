# ESP8266-BME280-ESPHome

First ESPHome project

## 2026-03-18 Motivation

I have an ESP8266 that connects to a BME280 sensor to measure and publish temperature, humidity and pressure via MQTT. One previous project <https://github.com/HankB/ESP8266_RTOS_BME280> no longer builds. Another using PlatformIO and based on the Arduino SDK <https://github.com/HankB/esp8266_MQTT_BME280> builds but has weird problems. Depending on the amount of serial I/O the project does, it either doesn;t run at all, runs but produces wildly wrong readings or produces reasonable readings for a bit before reverting to the wild readings. IAC, I've been meaning to take a look at ESPHome and that seems particularly suitable because I'll be integrating the readings with HomeAssistant.

## 2026-03-18 Plan

Explore, get something working. Add MQTT publishing and then test, test and test.

## 2026-03-18 Toolchain

Install per instructions at <https://esphome.io/guides/installing_esphome/>. The `venv` is in `/esp/venv`. Editing using VS Code (but not the Espressif or PlatformIO Extensions.) This host is running Deboan Linux (Trixie.)

## 2026-03-18 Usage and build

* Project home is `~/home/hbarta~/Programming/ESP8266/ESP8266-BME280-ESPHome`.

```text
esphome wizard ESP8266-BME280-ESPHome.yaml # and answer the connections
esphome run ESP8266-BME280-ESPHome.yaml # Connect ESP, verify `/dev/ttyUSB0` and proceed
```

```text
Hard resetting via RTS pin...
INFO Successfully uploaded program.
INFO Starting log output from /dev/ttyUSB0 with baud rate 115200
[16:44:40.053][D][wifi:1382]: Found networks:
[16:44:40.061][I][wifi:1353]: - 'Farm_IoT' (6E:5A:B0:46:DA:5A) ▂▄▆█ Ch: 7 -41dB P:0
[16:44:40.069][D][wifi:1739]: Retry phase: INITIAL_CONNECT → SCAN_CONNECTING
[16:44:40.082][I][wifi:1064]: Connecting to 'Farm_IoT' (6E:5A:B0:46:DA:5A) (priority 0, attempt 1/2 in phase SCAN_CONNECTING)...
[16:44:41.549][I][wifi:1475]: Connected
[16:44:41.552][D][wifi:1492]: Disabling AP
[16:44:41.558][C][wifi:1202]:   IP Address: 192.168.10.199
[16:44:41.561][C][wifi:1213]:   SSID: 'Farm_IoT'
[16:44:41.564][C][wifi:1213]:   BSSID: 6E:5A:B0:46:DA:5A
[16:44:41.566][C][wifi:1213]:   Hostname: 'bme280-test'
[16:44:41.572][C][wifi:1213]:   Signal strength: -44 dB ▂▄▆█
[16:44:41.572][C][wifi:1213]:   Channel: 7
[16:44:41.574][C][wifi:1213]:   Subnet: 255.255.255.0
[16:44:41.577][C][wifi:1213]:   Gateway: 192.168.10.1
[16:44:41.577][C][wifi:1213]:   DNS1: 192.168.10.1
[16:44:41.580][C][wifi:1213]:   DNS2: 0.0.0.0
[16:44:41.586][W][component:462]: wifi cleared Warning flag
[16:45:34.093][I][safe_mode:071]: Boot seems successful; resetting boot loop counter
```

Confirm that AP lists 'bme280-test` and it does.

## 2026-03-19 BME280 sensor

* <https://esphome.io/components/i2c/>
* <https://esphome.io/components/sensor/bme280/>
* <https://esphome.io/components/sensor/#example-converting-celsius-to-fahrenheit> "I’m not planning on making converting between the two simple (😉)" That's irritating.

```text
i2c:
  sda: D3
  scl: D4
  scan: true

sensor:
  - platform: bme280_i2c
    temperature:
      name: "BME280 Temperature"
    pressure:
      name: "BME280 Pressure"
    humidity:
      name: "BME280 Humidity"
```

and uploaded OTA with a message in the log:

```text
[08:37:24.753][I][i2c.arduino:088]: Results from bus scan:
[08:37:24.753][I][i2c.arduino:094]: Found device at address 0x76
[08:37:24.753][C][bme280_i2c.sensor:025]:   Address: 0x77
[08:37:24.753][C][bme280.sensor:180]: BME280:
[08:37:24.753][E][bme280.sensor:183]: Communication failed
```

Now make

```text
sensor:
  - platform: bme280_i2c
    temperature:
      name: "BME280 Temperature"
    pressure:
      name: "BME280 Pressure"
    humidity:
      name: "BME280 Humidity"
    address: 0x76
```

And it works.

```text
[08:43:54.744][D][sensor:118]: 'BME280 Temperature' >> 15.1 °C
[08:43:54.749][D][sensor:118]: 'BME280 Pressure' >> 993.4 hPa
[08:43:54.756][D][sensor:118]: 'BME280 Humidity' >> 35.1 %
```

## 2026-03-18 HomeAssistant

HA already found the device and I was able to "add it" in the settings got ESPHome, but it comes up with "No devices or entities". After upgrading HASS from 2025.3.3 to 2026.3.3 HASS is pulling the values from the ESPHome device automatically.

## 2026-03-19 TODO

* MQTT

## 2026-03-19 Results

It is working and was pretty straight forward to configure and install. I will definitely consider using it for future HASS sensors if the MQTT publishing can be as easily conigured. I also want NTP support.

## Errata

* 2026-03-19 Cannot publish the entire configuration file at this time because it includes sensitive information.
