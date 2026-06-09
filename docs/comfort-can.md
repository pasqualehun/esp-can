# Comfort CAN research

The Comfort CAN bus is not the same as the OBD diagnostic CAN bus.

OBD diagnostic CAN:

```text
OBD pin 6  = CANH
OBD pin 14 = CANL
500 kbps
```

Comfort CAN:

```text
Expected speed: 100 kbps
Expected location: near the OCU under the seat
```

Known Comfort CAN wire colors from OVMS documentation:

| Signal | Wire color |
|---|---|
| Comfort CAN-H | orange/green |
| Comfort CAN-L | orange/brown |

## Planned Comfort CAN features

- SOC from broadcast frames
- Range from broadcast frames
- Climate / pre-heating control
- OCU / ring awake state
- Charging state
- Door / lock state

## Known broadcast candidates

| CAN ID | Possible meaning |
|---:|---|
| `0x61A` | SOC |
| `0x52D` | estimated range |
| `0x575` | key / ignition state |
| `0x571` | 12V voltage |
| `0x381` | locked state |
| `0x470` | doors / trunk / hood |
| `0x3E1` | HVAC running |
| `0x3E3` | cabin temperature |
| `0x527` | outdoor temperature |
| `0x61C` | charge detection |
| `0x65D` | odometer candidate |

## SOC and range candidates

SOC from `0x61A`:

```text
SOC = byte[7] / 2.0
```

Range from `0x52D`:

```text
range = byte[0]
if byte[1] == 0x41:
    range += 255
```

## Wakeup / OCU emulation research

Initial wakeup candidate:

```text
ID:   0x69E
Data: 14 51
```

Ring-related frames:

```text
ID:   0x43D
Data: 1D 02 02 00 00 14 00 00

ID:   0x43D
Data: 00 01 02 04 00 14 00 00
```

OCU heartbeat candidates:

```text
ID:   0x5A9
Data: 00 00 00 00 00 00 00 00

ID:   0x5A7
Data: 00 16 00 00 00 00 00 00

ID:   0x5A7
Data: 60 16 00 00 00 00 00 00
```

Climate / pre-heating control has not been implemented in this project yet. It should be tested carefully and only after Comfort CAN wakeup and heartbeat handling is understood.
