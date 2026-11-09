ESP32 SOIL MOISTURE MONITORING SYSTEM
with Live Web Dashboard and Telegram Bot Alerts

Project By: PILLA JOSHI

------------------------------------------------------------
ABOUT
------------------------------------------------------------
An IoT-based soil moisture monitoring system built around the
ESP32 and a two-prong soil moisture probe with an LM393 module.
The ESP32 reads the sensor, converts the raw ADC value into a
moisture percentage, and shares it in two ways at the same time:

1. A live web dashboard hosted on the ESP32 itself
   (no external server or cloud account needed).
2. A Telegram bot that sends automatic dry and recovery alerts
   and answers on-demand commands.

------------------------------------------------------------
FEATURES
------------------------------------------------------------
- Live moisture gauge with a colour-coded condition label
  (red = dry, green = moist, blue = wet)
- Device statistics: raw ADC value, Wi-Fi signal (dBm), average,
  minimum and maximum moisture, uptime
- 20-minute history chart (last 120 readings)
- Alert log and a red warning banner when the soil is dry
- Works on phone and desktop, in light and dark mode
- Telegram alerts with hysteresis and a 30-minute cooldown to
  avoid spam
- Telegram commands: /start, /moisture, /status
- Only the authorised chat ID can read data

------------------------------------------------------------
HARDWARE REQUIRED
------------------------------------------------------------
- ESP32 development board
- Soil moisture probe with LM393 module
- Jumper wires and breadboard
- USB cable and 5 V supply
- Wi-Fi network with internet access

------------------------------------------------------------
SOFTWARE AND LIBRARIES
------------------------------------------------------------
- Arduino IDE with ESP32 board package
- WiFi.h
- WebServer.h
- WiFiClientSecure.h
- UniversalTelegramBot (Brian Lough)
- ArduinoJson 6.21.x (Benoit Blanchon)
- Telegram app, @BotFather and @myidbot

------------------------------------------------------------
PIN CONNECTIONS
------------------------------------------------------------
Sensor pin    ESP32 pin    Description
VCC           3V3          Power supply (3.3 V)
GND           GND          Common ground
AOUT          GPIO34       Analog output to ADC input

Note: GPIO34 is an ADC1 pin, which keeps working while Wi-Fi is
active. ADC2 pins are not reliable when Wi-Fi is on.

------------------------------------------------------------
SETUP
------------------------------------------------------------
1. Create the Telegram bot with @BotFather and copy the bot
   token. Get your chat ID from @myidbot.
2. Install UniversalTelegramBot and ArduinoJson (6.21.x) from
   the Arduino Library Manager.
3. Wire the sensor: VCC to 3V3, GND to GND, AOUT to GPIO34.
4. In the user-settings section of the sketch, enter the Wi-Fi
   name and password, the bot token and the chat ID.
5. Calibrate: note the raw value in open air (dry) and in water
   (wet), then enter them as the calibration values.
6. Upload the sketch to the ESP32, open the Serial Monitor at
   115200 baud to see the IP address, then open that address in
   a browser on the same Wi-Fi network.

------------------------------------------------------------
CALIBRATION
------------------------------------------------------------
Moisture (%) = (Raw - 3200) x 100 / (1400 - 3200), limited to
0-100.

Dry (probe in open air)         raw 3200 = 0 %
Wet (probe in a glass of water) raw 1400 = 100 %

These values are for the sensor used in this project. Measure
your own sensor and update them.

------------------------------------------------------------
MOISTURE LEVELS AND ALERTS
------------------------------------------------------------
Below 30 %      Dry - needs water (alert triggered)
30 % to 59 %    Moist (good)
60 % and above  Wet

- Dry alert is sent when moisture drops below 30 %. It repeats
  only after the 30-minute cooldown.
- Recovery message is sent when moisture rises above 40 %.
- Between 30 % and 40 % no new message is sent (dead band).

------------------------------------------------------------
TIMING
------------------------------------------------------------
Sensor check interval     10 seconds
Telegram polling          1 second
Dashboard refresh         5 seconds
History buffer            120 readings (about 20 minutes, in RAM)
Samples per reading       20 (5 ms apart)

------------------------------------------------------------
WEB ENDPOINTS
------------------------------------------------------------
/       Dashboard page (HTML, CSS and JavaScript from flash)
/data   Current data in JSON format

------------------------------------------------------------
TELEGRAM COMMANDS
------------------------------------------------------------
/start      Welcome message with commands and dashboard address
/moisture   Current moisture %, condition and raw ADC value
/status     Moisture details, Wi-Fi signal, uptime, dashboard address

Automatic messages: online message on start-up, dry alert,
recovery message.

------------------------------------------------------------
TEST RESULTS
------------------------------------------------------------
Situation                 Raw ADC   Moisture   Condition
Sensor dry / out of soil  4095      0 %        Dry - alert sent
Dry soil                  2855      19 %       Dry
Moist soil                2141      58 %       Moist (good)
Wet soil                  1969      68 %       Wet - recovery sent

------------------------------------------------------------
LIMITATIONS
------------------------------------------------------------
- Dashboard works only on the same Wi-Fi network unless port
  forwarding or a VPN is set up.
- History is stored in RAM and is lost on restart.
- Resistive probes can corrode over time in wet soil.
- Calibration values depend on the sensor and soil type.
- The system only monitors; it does not water the plant.

------------------------------------------------------------
FUTURE SCOPE
------------------------------------------------------------
- Relay and water pump for automatic watering
- LED or buzzer for local alerts
- Long-term data storage (cloud database or SD card)
- DHT11 temperature and humidity sensor
- Battery and solar power with deep-sleep mode
- Change thresholds from the dashboard or Telegram

------------------------------------------------------------
REFERENCES
------------------------------------------------------------
- Espressif Systems, ESP32 Series Datasheet and Arduino-ESP32
  documentation
- Brian Lough, UniversalTelegramBot library
- Benoit Blanchon, ArduinoJson library
- Telegram Bot API: core.telegram.org/bots/api
