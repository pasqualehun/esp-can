# ESPHome notes

## Recommended automatic polling

For normal dashboard use, poll only the essential values:

- Battery SOC
- Battery temperature
- Range display

Suggested interval:

```text
60 seconds
```

CAC / SOH should be requested manually or very rarely.

## Padding

Requests work with `0x00` padding, but `0xAA` padding matches observed responses and community examples:

```text
03 22 02 8C AA AA AA AA
```

## OTA notes

OTA updates may be unstable in the car if the ESP32 is powered directly from a 3.3V buck converter.

Observed behavior:

- USB power: OTA works
- In-car power: OTA may upload but old firmware may still boot or OTA may fail

Recommended permanent setup:

```text
OBD 12V -> automotive 5V buck -> ESP32 5V input
```

## Debug CAN catch-all

ESPHome requires `can_id` for `on_frame`.

A catch-all debug handler can be done with:

```yaml
on_frame:
  - can_id: 0
    can_id_mask: 0
    then:
      - lambda: |-
          // debug all frames
```

Be careful: logging every CAN frame can slow down the CAN handler and cause dropped ISO-TP frames.

## ISO-TP performance

Do not log every ISO-TP consecutive frame.

Recommended approach:

- First frame: initialize buffer and send flow control
- Consecutive frames: append bytes only
- Complete response: log and decode once
