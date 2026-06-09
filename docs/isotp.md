# ISO-TP / CAC / SOH

The CAC / SOH data uses an ISO-TP multi-frame response.

## Request

```text
CAN ID: 0x7E5
Data:   03 22 74 CB AA AA AA AA
```

## First response frame

```text
CAN ID: 0x7ED
Data:   10 5B 62 74 CB ...
```

`0x10` means ISO-TP first frame.

`0x5B` means the complete response length is 91 bytes.

`62 74 CB` is the positive response to `22 74 CB`.

## Flow control frame

After receiving the first frame, send a flow control frame:

```text
CAN ID: 0x7E5
Data:   30 00 14 00 00 00 00 00
```

Meaning:

```text
30 = Continue To Send
00 = block size unlimited
14 = STmin 20 ms
```

The 20 ms STmin was used to avoid losing frames in ESPHome while logging and processing CAN frames.

## Example assembled payload

The first bytes of the assembled payload:

```text
62 74 CB 29 02 ...
```

Decode:

```text
0x2902 = 10498
CAC = 10498 / 100 = 104.98 Ah
SOH estimate = 104.98 / 120 * 100 = 87.5 %
```

## Notes

The SOH value is an estimate calculated from CAC. It may not exactly match the value shown by official VW/Skoda diagnostic tools.

Recommended naming:

- `BMS CAC`
- `BMS SOH Estimate`

Do not poll CAC/SOH frequently. It is a multi-frame diagnostic response and is not needed for normal dashboard updates.
