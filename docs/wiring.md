# Wiring

## OBD2 pins

| OBD2 pin | Function |
|---:|---|
| 16 | +12V battery |
| 5 | Signal ground |
| 6 | CAN High |
| 14 | CAN Low |

Pin 4 is chassis ground. Pin 5 is signal ground. For this project, pin 5 is preferred.

## ESP32 to SN65HVD230

| ESP32 | SN65HVD230 |
|---|---|
| GPIO33 | TXD |
| GPIO32 | RXD |
| 3V3 | VCC |
| GND | GND |

Do not connect ESP32 GPIO pins directly to CANH/CANL.

## SN65HVD230 to OBD2

| SN65HVD230 | OBD2 |
|---|---|
| CANH | Pin 6 |
| CANL | Pin 14 |
| GND | Pin 5 / common ground |

## Power

Development setup:

```text
USB laptop / power bank -> ESP32
OBD pin 5 -> ESP32 GND
OBD pin 6 -> CANH
OBD pin 14 -> CANL
```

Permanent setup:

```text
OBD pin 16 (+12V)
   -> fuse
   -> automotive-grade buck converter
   -> ESP32 power input

OBD pin 5 (GND)
   -> ESP32 GND
   -> SN65HVD230 GND
```

A 5V buck converter feeding the ESP32 board 5V input is preferred over directly feeding 3.3V, because WiFi and OTA updates can create current spikes.

## Termination resistor

Many SN65HVD230 modules include a 120 ohm termination resistor.

For a real vehicle CAN bus, the vehicle is already terminated. Extra termination is usually not needed and may load the bus unnecessarily.

Recommended:

- For vehicle OBD connection: remove or disable the 120 ohm termination resistor.
- For bench testing with two CAN nodes only: use 120 ohm at each physical end of the bus.
