# CMozGlow has moved

The CMozGlow library now lives in its own repository:

### 👉 https://github.com/cmoz/CMozGlow

That is the single source of truth — all source, examples, documentation and
releases are maintained there.

## Install

**PlatformIO** — add one line to `platformio.ini`:

```ini
lib_deps = cmoz/CMozGlow @ ^1.2.2
```

**Arduino IDE** — open *Tools → Manage Libraries…*, search for **CMozGlow**, click Install.

## What is it?

A beginner-friendly WS2812B / NeoPixel library for ESP32 boards (S3, S2, C3 and
classic), built for the CMoz ESP32-S3 Mini and wearable projects:

- Plain-English error messages — no more silent failures
- Automatic power budgeting to protect small batteries
- Live status pixel (green = happy, red = error)
- Async RMT engine and optional dual-core AutoPilot mode
- Ten fashion-tech effects, including a Morse-code messenger

Full documentation, examples and the live browser simulator are linked from the
[CMozGlow repository](https://github.com/cmoz/CMozGlow).

---

Christine Farion · [CMozMaker](https://www.youtube.com/@CMozMaker) · [tinkertailor.ca](https://www.tinkertailor.ca)
