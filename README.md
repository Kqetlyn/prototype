# TorqueTrace prototype (v4)

A tablet web app for aircraft MRO that pins every torque reading to the right bolt, checks tools before and after use, error-proofs the job, and hands unfinished work to the next shift. School project concept for SIAEC operations, answering: *How can smart tools (e.g. digital torque wrenches, AR-assisted repair manuals) improve accuracy, efficiency and safety in MRO?*

**Live version:** https://kqetlyn.github.io/prototype/ (open in Chrome on an Android tablet)

## Core loop

**Scan the tool → AR guides each bolt → the wrench records it → verified handover.** The app is organised in four steps (Prepare, Torque with AR, Verify, Hand over), and every feature is tagged with the pillar of the problem statement it serves: Accuracy, Efficiency or Safety.

## Features

**AR and gestures (tablet camera)**
- Panel overlay: each bolt shows its status (green in spec, red out of tolerance, orange out of order, purple suspect).
- AR task-card manual: the current step and its key values sit in the camera view; the full task card opens over the panel (Manual button or Victory gesture).
- Gestures (6 core): open palm = gestures on/off · point = select bolt · swipe = next/previous · Victory = manual · OK sign = confirm · thumbs up = finish. Extras: fist = photo · two hands, fingers up = jump to that bolt.
- Digital twin view (2D): past visits' readings painted on each bolt, drift colours, repeat-problem bolts, running-torque trend.
- Hazard markers on the panel and live safety alerts in view.

**Smart tool (Bluetooth or simulated)**
- QR/NFC pairing: scan the tool sticker, the app checks calibration date, cycle count, range and drop/overload history, then connects.
- Tool history log: cycles since calibration (ISO 6789 rule of thumb: 12 months or about 5,000 cycles), drops, overloads, jobs.
- Reverse traceability: if a wrench fails its check, every job and bolt it touched since its last good calibration is listed and flagged.
- Self-locking nut correction: running torque is measured and added to the target (AC 43.13-1B practice); worn nuts are caught.
- Crowfoot correction: scan the adapter, the wrench setting is calculated.
- Live torque curve, battery, signal and cycles.
- Automatic data log with CSV export; replacement nut lots logged by scanning.

**Error-proofing and safety**
- Work order with pre-task safety check: tools stay locked until it is done.
- Bolt order lock: readings on the wrong bolt are rejected.
- Calibration and cycle reminders against the work order.
- Close-out: every checked-out tool must be returned before sign-off.
- Independent inspection mode (EASA 145.A.48 error-capturing): a different person confirms bolt by bolt, can reject for rework, then signs.
- Heat stress alerts using MOM WBGT bands for outdoor work.
- Torque stripe photo check (colour only; alignment needs a trained model).

### New in v4

- **3D twin.** Tap "3D twin" to open a 3D model of panel 571AB on a curved fuselage section. Drag to turn it, pinch or scroll to zoom, and tap a bolt to see today's reading and every past visit. Ring colours follow the bolt status; a pulsing purple ring marks a repeat problem; yellow markers are hazards. With gestures on: pinch your thumb and index finger and move your hand to turn the model; move two hands apart or together to zoom.
- **Place in room (AR).** Puts the 3D twin on a table or the floor with WebXR, so you can walk around it and tap bolts. Needs Chrome on an Android tablet that supports ARCore. iPad and iPhone Safari do not support WebXR AR.
- **What's this?** Point the camera at a component. With a trained parts model in `models/components/` it names the part (access panel, wheel, pitot probe, static port, antenna) and shows short notes. Without one it uses a general demo model that knows aircraft and everyday objects. It recognises the type of part; the QR code still confirms which panel. See [models/README.md](models/README.md) to train your own model with Teachable Machine.
- Keys: 3 = 3D twin, W = What's this?

## Test stickers

Print `stickers/torquetrace_test_stickers.png`. Each QR code holds plain text:

| Code | Expected result |
|---|---|
| `TT:TW-0042` | Wrench passes all checks |
| `TT:TW-0044` | Wrench passes (backup) |
| `TT:TW-0043` | Fails: calibration overdue |
| `TT:TW-0061` | Fails: cycle limit reached |
| `TT:TW-0050` | Fails: wrong torque range |
| `TT:CF-0012` | Crowfoot, E 30 mm |
| `TT:CF-0025` | Crowfoot, E 50 mm |
| `TT:LT-2611` | Replacement nut lot |

## Demo script (about 3 minutes)

1. Scan TW-0043 (blocked: calibration overdue), then TW-0042.
2. Tick the safety check, connect, and pull bolt 1. Try bolt 5: the order lock rejects it.
3. Choose "Worn nut" on bolt 2, log the new nut, torque again.
4. Simulate a drop, record "Failed": reverse traceability lists the suspect jobs and bolts.
5. Switch to TW-0044, finish all bolts, return tools, sign off.
6. Inspector I-5678 confirms bolts (OK sign or tap), ticks the checks, signs.
7. Turn on Twin view; set WBGT to 33.5 °C to show the heat alert; export the CSV.
8. Open the 3D twin, tap bolt 7 to show its repeat problem, then "Place in room" on the Android tablet.
9. Turn on What's this? and point at a part (needs your trained parts model).

## What is simulated

Wrench readings, running torque, live curves, battery and signal, drops and overloads, the tool register and history, bench check results, the heat sensor, badge scans, the twin's past visits, the 3D model (a simple panel, not manufacturer CAD) and the panel position (fixed overlay; a later version anchors it to markers on the real panel). Torque values, minimum running torque, bolt order, part lots and aircraft registrations are demo values, not from a maintenance manual.

## Real Bluetooth tools

- **Norbar NorTronic:** open plain-text Bluetooth protocol (set targets, read results, stream live torque, read calibration date, disable tool). Best fit.
- **Tohnichi CEM3-G-BTD / BTA:** duplex, limits sent to the wrench and results back. BTLA models use HID over GATT, which Web Bluetooth blocks; use keyboard mode instead.
- **Stahlwille MANOSKOP 766 DAPTIQ HCT:** Bluetooth and USB, open protocol via the HCT Windows app, HID mode.
- **Sylvac / Mitutoyo Bluetooth gauges:** HID keyboard mode types readings into a focused field.
- **Kestrel 5400 Heat Stress Tracker:** WBGT over Bluetooth via its LiNK app.

## ESP32 wrench (stage 5)

Advertise as `TT-TW0042` with the Nordic UART service `6e400001-b5a3-f393-e0a9-e50e24dcca9e`. Send lines on TX: `LV:12.4` live samples, `RT:2.6` running torque, `TQ:27.8` peak at the wrench drive, `BT:84` battery, `EV:DROP` / `EV:OVER`, and `ID:TW-0042` when the app writes `ID?`.

## Roadmap

1. ~~Task card, panel, simulated wrench, handover~~
2. ~~Camera view and gestures~~
3. ~~QR tool scan with checks~~
4. ~~v2: tool history, traceability, nut and crowfoot correction, error-proofing, inspection, twin, alerts, data log~~
5. ~~v3: core-loop steps, pillar tags, AR task-card manual~~
6. ~~v4: 3D twin, AR placement, What's this? part recognition~~
7. AI chat: questions about the task card, voice defect logging, smarter handover
8. Real ESP32 or NorTronic wrench over Bluetooth
9. Marker-anchored AR on a real panel

## Test status (v4)

Tested in headless Chrome: the 3D twin renders, turns, zooms and picks bolts; pinch and two-hand zoom gestures (synthetic hand landmarks); the parts-model pipeline with a small test model. Not yet tested on a real device: AR placement (WebXR), and gestures with a real hand.
