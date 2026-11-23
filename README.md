# TorqueTrace prototype

A tablet web app for aircraft MRO that pins every torque reading to the right bolt, checks the tool before use, and hands unfinished work to the next shift. Built as a school project concept for SIAEC operations.

**Live version:** https://kqetlyn.github.io/prototype/ (open in Chrome on an Android tablet)

## What it does

- **Tool check:** scan the QR sticker on the wrench (live with the camera, or from a photo). The app looks the tool up in a tool register, checks the calibration date and torque range, then connects.
- **Torque tracking:** each reading is pinned to the selected bolt. Green = in spec, red = over or under, orange = torqued out of order (the app sends you back to the skipped bolt).
- **Gestures (camera):** open palm, hold = gestures on/off · point = select bolt · swipe = next/previous bolt · fist, hold = evidence photo · thumbs up, hold = finish task.
- **Finish and sign-off:** blocked while any bolt is open or flagged; sign-off by (simulated) staff badge.
- **Handover summary:** progress, open bolts, faults and where the next shift should continue.

## Test stickers

Print `stickers/torquetrace_test_stickers.png`. Each QR code holds plain text:

| Code | Expected result |
|---|---|
| `TT:TW-0042` | Passes: calibration in date, range covers 25 N·m |
| `TT:TW-0043` | Fails: calibration overdue |
| `TT:TW-0050` | Fails: wrong torque range |

## What is simulated

Wrench readings ("Simulate pull"), the tool register, the staff badge scan, and the panel position (the overlay is fixed on screen; a later version anchors it to markers on the real panel). Torque values and the bolt order are demo values, not from a maintenance manual.

## Requirements

- Chrome on Android, over https (GitHub Pages provides this). iPads do not support Web Bluetooth or Web NFC.
- Camera and gestures do not work inside the Claude artifact preview; use the GitHub Pages link.
- The gesture model (MediaPipe) downloads on first use.

## ESP32 wrench (stage 5)

Advertise as `TT-TW0042` with the Nordic UART service `6e400001-b5a3-f393-e0a9-e50e24dcca9e`. Send lines on the TX characteristic:

- `TQ:25.3` for a torque reading
- `ID:TW-0042` when the app writes `ID?`

Switch the wrench mode to Bluetooth in the app before connecting.

## Roadmap

1. ~~Task card, 12-bolt panel, simulated wrench, handover summary~~
2. ~~Camera view and gesture controls~~
3. ~~QR tool scan with calibration and range checks~~
4. AI chat: questions about the task card, voice defect logging, smarter handover
5. Real ESP32 wrench over Bluetooth
