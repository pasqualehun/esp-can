# Known OBD DIDs

OBD diagnostic CAN runs at 500 kbps.

## Request/response IDs

| ECU | Request ID | Response ID |
|---|---:|---:|
| BMS | `0x7E5` | `0x7ED` |
| MFD | `0x714` | `0x77E` |
| Charge Management | `0x765` | `0x7CF` |

## Known DIDs

| Value | ECU | DID | Decode |
|---|---|---:|---|
| SOC absolute | BMS | `0x028C` | `raw / 2.5` |
| Battery voltage | BMS | `0x1E3B` | `u16 / 4` |
| Battery current | BMS | `0x1E3D` | `((u16 - 2044) / 4) * -1` |
| Battery temperature | BMS | `0x2A0B` | `s16 / 64` |
| Cell max voltage | BMS | `0x1E33` | `u16 / 4096` |
| Cell min voltage | BMS | `0x1E34` | `u16 / 4096` |
| CAC / SOH data | BMS | `0x74CB` | ISO-TP multi-frame |
| Range display | MFD | `0x22E0` | `u16 km` |
| Range energy | MFD | `0x22E4` | `u16 / 10 kWh` |
| Charge management SOC | Charge Management | `0x1DD0` | `raw / 2` |
| MFD odometer candidate | MFD | `0x2203` | likely `uint24 km`, needs verification |

## SOC request example

Request:

```text
CAN ID: 0x7E5
Data:   03 22 02 8C AA AA AA AA
```

Response:

```text
CAN ID: 0x7ED
Data:   04 62 02 8C 99 AA AA AA
```

Decode:

```text
0x99 = 153
153 / 2.5 = 61.2 %
```

## Range request example

Request:

```text
CAN ID: 0x714
Data:   03 22 22 E0 AA AA AA AA
```

Response:

```text
CAN ID: 0x77E
Data:   05 62 22 E0 00 B5 AA AA
```

Decode:

```text
0x00B5 = 181 km
```
