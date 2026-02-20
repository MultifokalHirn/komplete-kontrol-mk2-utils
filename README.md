# qKontrol (Komplete Kontrol MK2 Utilities)

qKontrol is a Qt desktop application for Linux, macOS, and Windows that directly configures **Native Instruments Komplete Kontrol MK2** keyboards (S49/S61/S88 MK2) over HID/USB, without depending on Native Instruments' host software.

It is intended for users who want full control over MIDI mappings, light guide behavior, display content, key zones, pedal behavior, and optional OSC-based DAW integration.

---

## Features

- Device discovery and direct HID communication with Komplete Kontrol MK2 hardware (vendor `0x17cc`, product IDs for 49/61/88-key MK2 models).
- Full mapping of:
  - 8 touch knobs
  - button banks/pages
  - keybed zones/splits
  - sliders and wheel behavior
  - pedal/tip/ring control modes
- Per-control MIDI channel/CC assignment options and mode-dependent parameter visibility.
- Color and display rendering customization for labels/value lines shown on the hardware screens.
- Runtime display drawing and updates (including parameter values and track/device status overlays).
- Preset system with save/load support using `.qcp` XML files.
- Embedded left/right display bitmaps stored in preset files as Base64 PNG blobs.
- Preset-folder browsing from hardware navigation buttons (next/previous preset in same directory).
- Optional OSC integration for DAW transport and plugin/device parameter workflows.
- OSC dialog + persistent OSC settings in user config (`~/.qkontrol/osc.ini`).
- Light guide / button backlight update support.
- Cross-platform Qt GUI application project (`qmake` + `.pro` file).

---

## Use cases

- Build a custom MK2 MIDI template for live performance, with zone splits and dedicated controls.
- Run the keyboard as a DAW controller in Linux environments where vendor tooling is limited.
- Assign transport functions (play/stop/record/click/loop) using OSC messages to Bitwig-compatible endpoints.
- Create per-song preset files and switch between them quickly from the keyboard’s preset buttons.
- Deploy a controller setup across multiple machines by copying `.qcp` preset files and `osc.ini`.
- Customize hardware screen visuals with your own left/right images and color themes.
- Operate Komplete Kontrol MK2 in plugin/parameter-focused mode with named parameter overlays.

---

## Screenshots and UI references

### Main display customization
![MK2 LCD preview](images/lcd.png)

### Button mapping and behavior
![Buttons view](images/buttons.png)

### Knob mapping
![Knobs view](images/knobs.png)

### Slider and wheel controls
![Sliders view](images/sliders.png)

### Key zones and splits
![Zones view](images/zones.png)

### Pedal mapping
![Pedals view](images/pedals.png)

### OSC configuration and workflow context
![OSC mode](images/osc.png)

### Plugin mode behavior
![Plugin mode](images/pluginmode.jpg)

### Display asset examples
![Display example](images/display.png)

---

## Supported hardware and platform scope

### Hardware
- Supported: Komplete Kontrol MK2 generation with dual displays (typically S49/S61/S88 MK2).
- Not supported: first-generation Komplete Kontrol keyboards, and M32/A-series hardware.

### Operating systems
- Linux
- macOS
- Windows

> Note: Functionality and setup complexity differ by OS due to HID access rules and driver behavior.

---

## Project architecture (for users and contributors)

- `source/main.cpp` initializes Qt app lifecycle and opens the main window.
- `source/qkontrol.cpp` implements most runtime behavior:
  - HID initialization/device open
  - UI wiring and widget updates
  - real-time input polling and value updates
  - key zone/button payload submission
  - OSC send/receive handling
  - preset save/load
- `source/oscdialog.cpp` handles OSC config dialog and persistence to `~/.qkontrol/osc.ini`.
- `source/qkontrol.ui` and `source/oscdialog.ui` define UI layout in Qt Designer format.
- `source/qkontrol.pro` defines Qt modules and per-platform library linkage.
- `source/79-udev-komplete.rules` is provided for Linux udev permissions setup.

---

## Installation and build manual

## 1) Runtime dependencies

### Linux runtime dependencies
- Qt5 runtime components
- `libhidapi` (libusb backend)
- `libusb-1.0`

Example install (Debian/Ubuntu families):

```bash
sudo apt-get update
sudo apt-get install -y libqt5gui5 libqt5test5 libhidapi-libusb0 libusb-1.0-0
```

### Linux build dependencies

```bash
sudo apt-get update
sudo apt-get install -y qtbase5-dev-tools qtbase5-dev build-essential libhidapi-dev libhidapi-libusb0 libusb-1.0-0-dev
```

### macOS dependencies
- Xcode command line/developer tools
- Qt (with Qt Creator if you prefer IDE build)
- Homebrew `libusb` and `hidapi`

```bash
brew install libusb hidapi
```

### Windows dependencies
- Native Instruments Komplete Kontrol MK2 drivers (for MIDI path)
- Zadig (to replace a specific USB device driver for this app's HID access path)
- MSYS2 packages (`mingw-w64-x86_64-hidapi`, `mingw-w64-x86_64-libusb`)
- Qt for Windows (build with MinGW 64-bit toolchain)

---

## 2) Build steps

### Linux (qmake + make)

From `source/`:

```bash
/usr/lib/x86_64-linux-gnu/qt5/bin/qmake
make -j"$(nproc)"
strip qkontrol
```

Depending on distro and Qt packaging, `qmake` may be available directly in PATH.

### macOS
- Open `source/qkontrol.pro` in Qt Creator and build.
- Optional redistribution step:

```bash
/path/to/macdeployqt qkontrol.app
```

### Windows
- Use Qt Creator with **MinGW 64-bit** kit.
- Build `source/qkontrol.pro`.
- Copy required Qt/MSYS2 DLLs next to `qkontrol.exe` if not using deployment tooling.

---

## 3) First start checklist

1. Connect and power on your Komplete Kontrol MK2.
2. Start qKontrol.
3. If no keyboard is found, verify USB cable/port and OS permissions/driver setup.
4. On macOS, allow qKontrol to terminate NI background agents if you need display access.
5. On Linux, if running as non-root fails, install the provided udev rule (see caveats section).

---

## General usage manual

## Workflow overview

A practical flow for most users:

1. Configure mappings and zones in UI tabs.
2. Click **Submit** to push current settings to device.
3. (Optional) Set screen images/colors.
4. Save to `.qcp` preset.
5. Load/test and iterate.
6. (Optional) enable OSC mode and connect to DAW endpoint.

## Mapping controls

### Knobs
- Configure mode, channel, CC, and descriptive labels.
- In plugin mode, knob labels can be updated from OSC-provided parameter names.
- Value rendering on displays updates in the HID poll loop.

### Buttons
- Button pages can be navigated and configured.
- Mode selection affects available settings.
- Backlight state is written via HID output packet updates.

### Key zones
- Define split/layer ranges using note-name spinboxes.
- Configure per-zone MIDI behavior and limits.
- Submit changes to apply immediately to hardware.

### Pedals / tip / ring
- Continuous and switch behavior are both supported.
- UI dynamically changes visible fields based on mode/switch-type selection.

### Display images and colors
- Left/right displays can be assigned image content.
- Text/value/parameter/divider colors are configurable.
- Current display image paths are serialized into presets as embedded PNG data.

---

## OSC mode manual

OSC is optional and can be used for:
- transport commands (play, stop, record, metronome/click, loop/repeat)
- device/parameter value send events
- receiving track/device/time data for display overlays
- receiving parameter names in plugin mode

### OSC setup steps

1. Open OSC dialog from the main window.
2. Configure:
   - enabled flag
   - target hostname/IP
   - local UDP port
   - remote UDP port
3. Apply changes (writes to `~/.qkontrol/osc.ini`).
4. Restart or re-open OSC config as needed.

### OSC behavior details

- qKontrol binds a UDP socket and listens for inbound messages.
- In plugin mode, control values can be sent out to remote OSC targets.
- Track/device/time strings are rendered onto bottom display lines when changed.

---

## Configuration and data storage

qKontrol uses two main configuration layers:

## A) Preset files (`.qcp`)

- Format: XML
- Saved via Save action in GUI
- Loaded via Load action in GUI
- Typical location: user-chosen path (commonly under home directory)

### `.qcp` contains
- widget values for spinboxes, checkboxes, radiobuttons, sliders, spansliders, comboboxes, toolboxes, tabwidgets
- display color values
- left and right screen bitmaps as Base64-encoded PNG
- selected line-edit description fields

### Access/edit strategy
- Preferred: edit through qKontrol UI and re-save.
- Advanced/manual: edit XML directly in a text editor and reload (ensure tags match widget object names).

## B) OSC settings (`~/.qkontrol/osc.ini`)

Stored as INI using Qt `QSettings` with keys:
- `enabled` (bool)
- `hostname` (string)
- `local` (int)
- `remote` (int)

### Access/edit strategy
- Preferred: OSC dialog.
- Advanced/manual: edit `~/.qkontrol/osc.ini` directly while app is closed.

---

## Caveats and common solutions

### 1) Linux permission errors (device access denied)

**Symptoms:** keyboard not found unless running as root.

**Fix:** install udev rule shipped in repo:

```bash
sudo cp source/79-udev-komplete.rules /etc/udev/rules.d/
sudo udevadm control --reload-rules
sudo udevadm trigger
# unplug/replug keyboard or reboot
```

### 2) macOS display communication blocked

**Symptoms:** mapping works partially but displays do not update.

**Cause:** NI background services holding device interfaces.

**Fix:** allow qKontrol prompt to kill NI services, or terminate relevant processes manually.

### 3) No live CC value visualization until DAW/app is open

**Symptoms:** expected value updates are absent.

**Cause:** keyboard firmware behavior: HID value data appears only while MIDI stream is actively consumed.

**Fix:** open a MIDI client/DAW that receives data from the keyboard.

### 4) Preset navigation buttons repeat unexpectedly

**Symptoms:** left/right key actions may repeat continuously.

**Fix/workaround:** reconnect device; verify no stuck hardware input state; retry with latest build.

### 5) Windows coexistence issues with NI software stack

**Symptoms:** HID path conflict / qKontrol cannot interact as expected.

**Fix:** follow driver separation workflow (NI driver for MIDI + Zadig replacement on target HID interface).

---

## Troubleshooting checklist

- Confirm USB connectivity and power state.
- Confirm device is MK2 generation.
- Check OS-level permissions/driver claim status.
- Verify OSC IP/port pair and network reachability.
- Test with a fresh preset and default mappings.
- Run from terminal to observe runtime logs/messages.

---

## Possible improvements / roadmap ideas

- Migrate build system to CMake for modern multi-platform packaging workflows.
- Add formal release binaries and platform-specific installers.
- Add automatic dependency/driver checks on startup (per OS).
- Improve OSC interoperability presets for popular DAWs beyond Bitwig.
- Add import/export for human-friendly JSON/YAML mapping profiles.
- Add schema validation and version migration for `.qcp` files.
- Improve error reporting for HID open/bind failures.
- Add UI-level test coverage and headless integration checks.
- Add robust debounce handling for repeated navigation key events.
- Improve documentation around Windows driver selection with exact VID/PID/interface screenshots.

---

## Licensing

- Main project is distributed under LGPL terms (see `LICENSE`).
- Included libQXT-derived widgets in `source/widgets/` include their own licensing texts.

---

## Code references

Helpful starting points in source:

- App entry point: `source/main.cpp`
- Main window/controller logic: `source/qkontrol.cpp`, `source/qkontrol.h`
- OSC settings dialog and persistence: `source/oscdialog.cpp`, `source/oscdialog.h`
- Build and link settings: `source/qkontrol.pro`
- Linux udev permissions rule: `source/79-udev-komplete.rules`
