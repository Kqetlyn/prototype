# Parts model for "What's this?"

The app looks for a Teachable Machine image model in `models/components/`. If it isn't there, "What's this?" uses a general demo model that knows aircraft and everyday objects, but not aircraft parts.

## Train it (no code)

1. **Take photos.** At the school hangar, photograph each part type from different angles, distances (about 0.5–2 m) and lighting. Aim for 50–100 photos per class. Hold the tablet the way you would during work.
2. **Include a Background class:** floor, hangar walls, plain skin with no parts, people, tool trolleys. This stops the model guessing a part when there isn't one.
3. Open [Teachable Machine](https://teachablemachine.withgoogle.com/train/image) → **Standard image model**.
4. Make one class per part. Use names containing these words so the app shows notes for them: `Access panel`, `Wheel`, `Pitot probe`, `Static port`, `Antenna`, `Background`.
5. Upload the photos and press **Train model**. Test it in the preview with photos you did **not** train on.
6. **Export model → TensorFlow.js → Download my model.** You get a zip with `model.json`, `weights.bin` and `metadata.json`.
7. Unzip the three files into `models/components/` in this repo, so you have `models/components/model.json` and so on. Commit and push. Reload the app on the tablet.

## Good to know

- The model recognises the **type** of part. Access panels look alike, so the QR code (or the stencilled panel number) still confirms **which** panel.
- More varied photos beat more identical photos. If it confuses two parts, add photos of both from the angles where it fails.
- Class names without notes still show their name. To add notes, edit the `PARTS` list in `index.html`.
- Only photograph aircraft and areas you're allowed to. Don't commit photos or models that show customer aircraft registrations or anything confidential.
