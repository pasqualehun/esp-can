# Skoda Citigo-e iV ESP32 OBD Telemetry

ESP32-based OBD/CAN telemetry project for the Skoda Citigo-e iV, Volkswagen e-Up, and Seat Mii electric.

The goal of this project is to expose useful EV battery and range data to Home Assistant using a low-cost ESP32 board and a CAN transceiver.

![Citigo ESP32 OBD setup](docs/citigo-obd-hardware.jpg)

## Current status

Working:

- OBD CAN physical connection
- UDS request/response
- BMS SOC
- BMS temperature
- MFD range
- BMS voltage/current
- Cell min/max voltage
- ISO-TP flow control
- CAC / SOH estimate

Recommended automatic polling:

- Battery SOC
- Battery temperature
- Range display

CAC / SOH should be requested manually or very rarely because it uses ISO-TP multi-frame communication.

## Supported vehicles

This project targets the VW Group small EV platform:

- Skoda Citigo-e iV
- Volkswagen e-Up
- Seat Mii electric

## Hardware

Tested hardware:

- LILYGO TTGO T8 V1.8 ESP32-WROVER
- ESP32-D0WD-V3
- 4 MB flash
- 8 MB PSRAM
- SN65HVD230 CAN transceiver module
- Mini560 DC-DC buck converter
- OBD2 connector

## Wiring overview

### ESP32 to SN65HVD230

| ESP32 | SN65HVD230 |
|---|---|
| GPIO33 | TXD |
| GPIO32 | RXD |
| 3V3 | VCC |
| GND | GND |

### SN65HVD230 to OBD2

| SN65HVD230 | OBD2 |
|---|---|
| CANH | Pin 6 |
| CANL | Pin 14 |

### Power

| OBD2 | Converter / ESP32 |
|---|---|
| Pin 16 | Buck converter VIN |
| Pin 5 | GND |

Recommended power setup:

```text
OBD pin 16 (+12V)
   -> buck converter
   -> ESP32 power input

OBD pin 5 (GND)
   -> ESP32 GND
   -> SN65HVD230 GND
```

For permanent installation, an automotive-grade 5V buck converter is recommended. Direct 3.3V powering can work, but OTA updates may become unstable if the voltage drops during WiFi activity.

## CAN settings

OBD diagnostic CAN:

```yaml
bit_rate: 500kbps
tx_pin: GPIO33
rx_pin: GPIO32
```

Known working request/response pairs:

| ECU | Request ID | Response ID |
|---|---:|---:|
| BMS | `0x7E5` | `0x7ED` |
| MFD | `0x714` | `0x77E` |
| Charge Management | `0x765` | `0x7CF` |

## Working values

| Sensor | Source | Status |
|---|---|---|
| Battery SOC | BMS | Working |
| Battery temperature | BMS | Working |
| Range display | MFD | Working |
| Battery voltage | BMS | Working |
| Battery current | BMS | Working |
| Battery power | calculated | Working |
| Cell min voltage | BMS | Working |
| Cell max voltage | BMS | Working |
| Cell voltage delta | calculated | Working |
| Charge management SOC | Charge Management ECU | Working |
| CAC | BMS ISO-TP | Working |
| SOH estimate | calculated from CAC | Working |

## Example values

Example values read from a Skoda Citigo-e iV:

| Value | Example |
|---|---:|
| Battery SOC | `60.0 %` |
| Battery temperature | `22.5 °C` |
| Range display | `181 km` |
| Range energy | `17.2 kWh` |
| Battery voltage | `317.0 V` |
| Battery current | `0.8 A` |
| Battery power | `0.24 kW` |
| Cell max voltage | `3.7922 V` |
| Cell min voltage | `3.7642 V` |
| Cell delta | `0.0281 V` |
| CAC | `104.98 Ah` |
| SOH estimate | `87.5 %` |

## Documentation

- [Wiring](docs/wiring.md)
- [Known OBD DIDs](docs/obd-dids.md)
- [ISO-TP / SOH decoding](docs/isotp.md)
- [Comfort CAN research](docs/comfort-can.md)
- [ESPHome notes](docs/esphome-notes.md)

## Files

- `citigo-can.yaml` - ESPHome configuration
- `secrets.example.yaml` - example ESPHome secrets file
- `docs/` - additional project documentation

## Safety notes

This project sends diagnostic CAN requests to the vehicle.

Important:

- Do not connect ESP32 GPIO pins directly to CANH/CANL.
- Always use a CAN transceiver such as SN65HVD230.
- Do not add extra 120 ohm termination to an already terminated vehicle CAN bus.
- Start with read-only diagnostic requests.
- Be careful with any write/control commands.
- Use fused power when powering from OBD pin 16.
- Do not connect 12V directly to ESP32.

## Next steps

- Reduce automatic polling to essential values
- Add robust timeout handling
- Add Comfort CAN support
- Investigate climate / pre-heating control

## Credits

This project is inspired by community research around:

- Open Vehicle Monitoring System
- VW e-Up / Skoda Citigo-e iV / Seat Mii electric reverse engineering
- ESPHome CAN bus integrations
- WiCAN / ESP32 CAN telemetry examples

## Disclaimer

Use at your own risk.

Connecting to a vehicle CAN bus can affect vehicle behavior if incorrect frames are transmitted. This project is experimental and is not affiliated with Skoda, Volkswagen, Seat, Espressif, or OVMS.
