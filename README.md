# qKontrol — Komplete Kontrol MK2 Utilities

> Qt desktop utility for **Native Instruments Komplete Kontrol MK2** keyboards — configure MIDI mappings, display content, key zones, and DAW integration directly over HID/USB, without vendor software.

[![License: LGPL](https://img.shields.io/badge/License-LGPL-blue.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/platform-Linux%20%7C%20macOS%20%7C%20Windows-lightgrey)](#supported-hardware--platforms)
[![Qt](https://img.shields.io/badge/Qt-5-green.svg)](https://www.qt.io/)

---

## Table of Contents

- [Features](#features)
- [Screenshots](#screenshots)
- [Supported Hardware & Platforms](#supported-hardware--platforms)
- [Getting Started](#getting-started)
  - [Dependencies](#dependencies)
  - [Build](#build)
  - [First Launch](#first-launch)
- [Usage](#usage)
  - [Workflow Overview](#workflow-overview)
  - [Mapping Controls](#mapping-controls)
- [OSC Integration](#osc-integration)
- [Configuration & Presets](#configuration--presets)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [Roadmap](#roadmap)
- [License](#license)

---

## Features

- 🔌 **Direct HID/USB communication** — no Native Instruments host software required (vendor `0x17cc`, S49/S61/S88 MK2 product IDs)
- 🎛️ **Full control mapping** — 8 touch knobs, button banks/pages, keybed zones/splits, sliders, pitch/mod wheels, and pedal/tip/ring modes
- 🎨 **Display customization** — per-control labels, value lines, color themes, and custom left/right screen images
- 💡 **Light guide & backlights** — update button and key backlights via HID output packets
- 💾 **Preset system** — save/load `.qcp` XML preset files with embedded Base64 display bitmaps; browse presets from hardware navigation buttons
- 📡 **Optional OSC integration** — DAW transport (play/stop/record/click/loop), parameter value forwarding, and plugin-mode parameter name overlays
- 🖥️ **Cross-platform Qt GUI** — built with `qmake` + `.pro`; runs on Linux, macOS, and Windows

---

## Screenshots

| Display customization | Button mapping | Knob mapping |
|:---:|:---:|:---:|
| ![LCD](images/lcd.png) | ![Buttons](images/buttons.png) | ![Knobs](images/knobs.png) |

| Sliders & wheels | Key zones | Pedal mapping |
|:---:|:---:|:---:|
| ![Sliders](images/sliders.png) | ![Zones](images/zones.png) | ![Pedals](images/pedals.png) |

| OSC configuration | Plugin mode | Display assets |
|:---:|:---:|:---:|
| ![OSC](images/osc.png) | ![Plugin mode](images/pluginmode.jpg) | ![Display](images/display.png) |

---

## Supported Hardware & Platforms

### Hardware

| Model | USB Vendor/Product | Status |
|---|---|---|
| Komplete Kontrol S49/S61/S88 **MK2** (dual displays) | `0x17cc` / MK2 product IDs | ✅ Supported |
| Komplete Kontrol first-generation | — | ❌ Not supported |
| M32 / A-series | — | ❌ Not supported |

### Operating Systems

| OS | Status |
|---|---|
| Linux | ✅ |
| macOS | ✅ |
| Windows | ✅ |

> **Note:** Setup complexity differs per OS due to HID access rules and driver behavior.

---

## Getting Started

### Dependencies

<details>
<summary><strong>Linux</strong></summary>

**Runtime**

```bash
sudo apt-get update
sudo apt-get install -y libqt5gui5 libqt5test5 libhidapi-libusb0 libusb-1.0-0
```

**Build**

```bash
sudo apt-get update
sudo apt-get install -y qtbase5-dev-tools qtbase5-dev build-essential libhidapi-dev libhidapi-libusb0 libusb-1.0-0-dev
```

</details>

<details>
<summary><strong>macOS</strong></summary>

- Xcode command-line tools
- [Qt](https://www.qt.io/download) (Qt Creator recommended)
- Homebrew packages:

```bash
brew install libusb hidapi
```

</details>

<details>
<summary><strong>Windows</strong></summary>

- Native Instruments Komplete Kontrol MK2 drivers (for MIDI path)
- [Zadig](https://zadig.akeo.ie/) to replace the target HID interface driver
- MSYS2 packages: `mingw-w64-x86_64-hidapi`, `mingw-w64-x86_64-libusb`
- [Qt for Windows](https://www.qt.io/download) with MinGW 64-bit toolchain

</details>

### Build

<details>
<summary><strong>Linux</strong> — qmake + make</summary>

From `source/`:

```bash
/usr/lib/x86_64-linux-gnu/qt5/bin/qmake
make -j"$(nproc)"
strip qkontrol
```

`qmake` may also be available directly in `$PATH` depending on your distro.

</details>

<details>
<summary><strong>macOS</strong> — Qt Creator</summary>

1. Open `source/qkontrol.pro` in Qt Creator.
2. Build the project.
3. *(Optional)* Deploy for redistribution:

```bash
/path/to/macdeployqt qkontrol.app
```

</details>

<details>
<summary><strong>Windows</strong> — Qt Creator (MinGW)</summary>

1. Open `source/qkontrol.pro` in Qt Creator with the **MinGW 64-bit** kit.
2. Build the project.
3. Copy required Qt/MSYS2 DLLs next to `qkontrol.exe` if not using deployment tooling.

</details>

### First Launch

1. Connect and power on your Komplete Kontrol MK2.
2. Start **qKontrol**.
3. If the keyboard is not detected, verify USB connectivity and OS-level permissions (see [Troubleshooting](#troubleshooting)).
4. **macOS only:** allow qKontrol to terminate NI background agents if display communication is needed.
5. **Linux only:** if running as non-root fails, install the provided udev rule (see [Troubleshooting](#troubleshooting)).

---

## Usage

### Workflow Overview

1. Configure mappings and zones across the UI tabs.
2. Click **Submit** to push the current settings to the device.
3. *(Optional)* Set custom screen images and color themes.
4. Save the setup to a `.qcp` preset file.
5. Load, test, and iterate.
6. *(Optional)* Enable OSC mode and connect to your DAW endpoint.

### Mapping Controls

#### Knobs
- Configure mode, MIDI channel, CC number, and descriptive labels.
- In plugin mode, knob labels update from OSC-provided parameter names.
- Display values refresh on each HID poll cycle.

#### Buttons
- Navigate and configure button pages.
- Mode selection controls which settings are exposed.
- Backlight state is written via HID output packets.

#### Key Zones
- Define split/layer ranges with note-name spinboxes.
- Configure per-zone MIDI behavior and note limits.
- Changes take effect immediately after **Submit**.

#### Pedals / Tip / Ring
- Supports both continuous and switch behavior.
- Visible fields adapt dynamically to the selected mode.

#### Display Images & Colors
- Assign custom images to the left and right displays.
- Configurable colors for text, values, parameters, and dividers.
- Display image paths are embedded as Base64 PNG data in preset files.

---

## OSC Integration

OSC is entirely optional. When enabled it supports:

- **Transport control** — play, stop, record, metronome/click, loop/repeat
- **Parameter forwarding** — send control values to a remote OSC target
- **Display overlays** — receive track/device/time strings and plugin parameter names

### Setup

1. Open the **OSC dialog** from the main window.
2. Fill in:
   - **Enabled** toggle
   - **Target hostname/IP**
   - **Local UDP port**
   - **Remote UDP port**
3. Apply — settings are persisted to `~/.qkontrol/osc.ini`.

### Behavior

- qKontrol binds a UDP socket on the configured local port and listens for inbound messages.
- In plugin mode, control values are forwarded to the remote OSC target.
- Incoming track/device/time strings are rendered on the bottom display lines.

---

## Configuration & Presets

qKontrol uses two configuration layers:

### Preset files (`.qcp`)

XML files saved and loaded from the GUI. They store:

- All widget values (spinboxes, checkboxes, radio buttons, sliders, comboboxes, etc.)
- Display color values
- Left/right screen bitmaps as Base64-encoded PNG
- Control label text

**Editing:** prefer the qKontrol UI. For advanced edits, open the XML in a text editor — tag names correspond to Qt widget object names.

### OSC settings (`~/.qkontrol/osc.ini`)

INI file managed by Qt `QSettings` with keys: `enabled`, `hostname`, `local`, `remote`.

**Editing:** prefer the OSC dialog. For manual edits, modify the file while the application is closed.

---

## Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| Keyboard not found (Linux, non-root) | Missing udev rule | Install the bundled rule: `sudo cp source/79-udev-komplete.rules /etc/udev/rules.d/ && sudo udevadm control --reload-rules && sudo udevadm trigger`, then replug |
| Displays don’t update (macOS) | NI background services holding device interfaces | Allow qKontrol to terminate NI services when prompted, or stop them manually |
| No live CC value updates | Keyboard firmware only sends HID data while MIDI is consumed | Open a DAW or MIDI client that receives from the keyboard |
| Preset navigation buttons repeat | Stuck hardware input state | Replug the device; retry with the latest build |
| HID path conflict (Windows) | NI driver and qKontrol competing for the same interface | Follow the driver separation workflow: NI driver for MIDI + Zadig for the target HID interface |

**General checklist:**

- Confirm USB connectivity and device power.
- Confirm the device is an MK2 (dual-display) model.
- Check OS-level permissions and driver claim status.
- Verify OSC IP/port pair and network reachability.
- Test with a fresh preset and default mappings.
- Run from a terminal to observe runtime log output.

---

## Contributing

Contributions are welcome! To get started:

1. Fork the repository and create a feature branch.
2. Build and test your changes locally (see [Getting Started](#getting-started)).
3. Open a pull request with a clear description of the change and its motivation.

### Project layout

| File | Purpose |
|---|---|
| `source/main.cpp` | Qt app lifecycle, main window initialization |
| `source/qkontrol.cpp` / `.h` | Main window: HID init, UI wiring, polling, OSC, preset I/O |
| `source/oscdialog.cpp` / `.h` | OSC config dialog and `~/.qkontrol/osc.ini` persistence |
| `source/qkontrol.ui` / `oscdialog.ui` | Qt Designer UI layouts |
| `source/qkontrol.pro` | Qt modules and per-platform library linkage |
| `source/79-udev-komplete.rules` | Linux udev permissions rule |

---

## Roadmap

- [ ] Migrate build system to CMake for modern multi-platform packaging
- [ ] Add formal release binaries and platform-specific installers
- [ ] Automatic dependency/driver checks on startup (per OS)
- [ ] Improve OSC interoperability presets for popular DAWs beyond Bitwig
- [ ] Import/export human-friendly JSON/YAML mapping profiles
- [ ] Schema validation and version migration for `.qcp` files
- [ ] Better error reporting for HID open/bind failures
- [ ] UI-level test coverage and headless integration checks
- [ ] Robust debounce handling for repeated navigation key events
- [ ] Windows driver-selection guide with exact VID/PID/interface screenshots

---

## License

- Main project: [LGPL](LICENSE)
- libQXT-derived widgets in `source/widgets/`: see per-file license headers
