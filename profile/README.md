# RPI-Seism

An open-source seismic monitoring system built around a Raspberry Pi and a 3-axis GD-4.5 geophone.  
Captures, archives, and visualises ground motion in real time — from raw ADC counts to calibrated waveforms.

---

## How it works

```
GD-4.5 Geophone
      │
      │ analog differential
      ▼
  ADS1256 ADC  ──SPI──▶  Arduino/ESP32
                              │
                              │ RS-422 · 250 kbaud
                              ▼
                       Raspberry Pi
                      ┌─────────────────────────────────┐
                      │  rpi-seism  (acquisition daemon) │
                      │  ├── RS-422 reader + heartbeat   │
                      │  ├── SDS MiniSEED archive         │
                      │  ├── STA/LTA trigger              │
                      │  ├── WebSocket live stream        │
                      │  └── Telegram notifications       │
                      └──────────────┬──────────────────-┘
                                     │
                    ┌────────────────┴──────────────┐
                    │                               │
                    ▼                               ▼
            rpi-seism-api                   rpi-seism-web
         (FastAPI archive browser)      (Angular live dashboard)
```

---

## Repositories

| Repository | Description |
|---|---|
| [daemon](https://github.com/rpi-seism/daemon) | Python acquisition daemon — RS-422 reader, SDS archiving, STA/LTA detection, WebSocket server, Telegram notifications |
| [reader](https://github.com/rpi-seism/reader) | Arduino/ESP32 firmware — ADS1256 ADC sampling, RS-422 streaming, runtime-configurable via settings handshake |
| [api](https://github.com/rpi-seism/api) | FastAPI archive browser — serves historical MiniSEED data with optional instrument response deconvolution |
| [web](https://github.com/rpi-seism/web) | Angular frontend — live waveform display, FFT spectrum, spectrogram waterfall, archive browser |
| [hardware](https://github.com/rpi-seism/hardware) | PCB & schematics — Coming soon |
| [stack](https://github.com/rpi-seism/stack) | Meta-repo — `docker-compose.yml` and submodules to deploy the full system in one command |

---

## Quick start

```bash
git clone --recurse-submodules https://github.com/rpi-seism/stack
cd stack
cp .env.example .env   # set UID, GID, serial port
docker compose up
```

The live dashboard will be available at `http://<pi-ip>` and the archive API at `http://<pi-ip>:8000`.

---

## Hardware

- **Geophone** — GD-4.5 (4.5 Hz natural frequency, 3-component)
- **ADC** — ADS1256 (24-bit, up to 30 kSPS)
- **MCU** — Arduino Uno / Nano / ESP32 / RP2040 / STM32 / Teensy
- **Interface** — RS-422 half-duplex at 250 kbaud via MAX422 or equivalent
- **Host** — Raspberry Pi 3/4 running Raspberry Pi OS

---

## Features

- Continuous 3-channel acquisition at up to 100 Hz
- SeisComp-compatible SDS MiniSEED archive with automatic midnight splitting
- StationXML generation with GD-4.5 PAZ instrument response and automatic epoch management
- STA/LTA earthquake detection with configurable thresholds
- Telegram alerts with a 120-second interactive waveform plot attached
- Live WebSocket stream with 4× decimation (25 Hz output)
- Archive browser with raw count and deconvolved (nm/s, nm, nm/s²) display
- Real-time FFT spectrum and spectrogram waterfall per channel
- Multi-platform Arduino firmware with runtime-configurable ADC gain and sample rate

---

## License

All repositories are released under the [GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.html).
