# RP2040 Zero – 20 Hz Altitude Logger


This project turns a Waveshare RP2040 Zero
 into a precise 20 Hz data logger using a BMP280 barometric pressure sensor. Logged data is written to onboard flash (via LittleFS) and can be dumped back over USB serial.

The key feature is exactly fixed 20 Hz sampling (0.05 s intervals), producing timestamps like:

time_s,alt_m
FLIGHT,0
0.00,0.12
0.05,0.08
0.10,-0.04
0.15,0.21
...


No drift or jitter is accumulated in the time column—values are always multiples of 0.05 s.

Features

Consistent 20 Hz logging: timestamps are index-based, always spaced by 0.05 s.

BMP280 sensor: altitude (m) logged relative to a quick baseline calibration.

Onboard flash storage (LittleFS): CSV files saved to /data.csv.

Flight separation: Each new log session begins with FLIGHT,n marker.

LED heartbeat: onboard WS2812 (NeoPixel) flashes different colors per mode (idle, logging, dumping, clearing).

Serial commands:

DUMP → print entire CSV back to serial, wrapped in [BEGIN_CSV] … [END_CSV]

CLEAR → erase the log file and reset flight counter

STATUS → report logging state, file size, and next flight number

Hardware

Board: Waveshare RP2040 Zero

Sensor: BMP280 (I²C, addr 0x76)

LED: Onboard WS2812 NeoPixel (GPIO16)

Log enable pin: GPIO14 (pull LOW = logging, HIGH = idle)

Pin configuration:

Function	Pin	Notes
I²C SDA	4	BMP280 data
I²C SCL	5	BMP280 clock
NeoPixel	16	Status heartbeat
Log control	14	Pull LOW to start logging
File Format

Logs are appended to /data.csv. Example contents:

time_s,alt_m
FLIGHT,0
0.00,0.12
0.05,0.08
0.10,-0.04
0.15,0.21
...
FLIGHT,1
0.00,-0.02
0.05,0.14
...


FLIGHT,n lines separate individual logging sessions.

Timestamps always increment in 0.05 s steps.

Altitudes are relative to baseline measured at logging start.

Building and Uploading

Install Arduino IDE 1.8.19+ or 2.x
.

Install the Earle Philhower RP2040 core
.

Select:

Board: Waveshare RP2040 Zero

Flash Size: 2MB (Sketch: 1MB, FS: 1MB)

USB Stack: Adafruit TinyUSB

Upload sketch via USB.

Connect Serial Monitor at 115200 baud.

Usage

Start logging: Pull GPIO14 LOW (e.g. jumper to GND).

Stop logging: Release GPIO14 (HIGH via pull-up).

Check status: Send STATUS over Serial.

Dump logs: Send DUMP over Serial → CSV is printed.

Clear logs: Send CLEAR over Serial.

LED Modes

Idle: Blue heartbeat (0.5 Hz)

Logging: Green heartbeat

Dumping: Yellow flash

Clearing: Red flash
