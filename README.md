# ESP32-Password-Manager

A small ESP32-based password-vault and TOTP interface with a TFT screen, physical buttons, a local Wi-Fi configuration portal, and USB HID keyboard output.

> **Important:** This repository contains firmware source code, not a finished or audited password manager. Review the code and secure all credentials before using it with real accounts.



## Features

- TFT-based menu for navigating saved entries.
- Password entries with name, username, and password fields.
- USB HID keyboard output for typing a username, password, or both into the focused field.
- TOTP code display with a 30-second countdown/progress bar.
- Wi-Fi time synchronization using configured NTP servers.
- Temporary Wi-Fi access point and browser-based password-management portal.
- Add, edit, and delete saved password entries.
- Password generator in the web portal.
- Persistent storage through the ESP32 `Preferences` API.

## Project structure

```text
passman/
├── passman.ino
├── README.md
└── assets/
    └── device.jpg
```

Replace `assets/device.jpg` with a clear photo of your assembled device. Keep the same filename so the image above continues to display.

## Hardware and libraries

The source uses the following Arduino libraries / platform components:

- `TFT_eSPI`
- `WiFi`
- `WebServer`
- `DNSServer`
- `Preferences`
- `TOTP`
- `USB.h`
- `USBHIDKeyboard.h`

The USB HID headers and API require a compatible ESP32 target with native USB support. Confirm your exact board and Arduino core version before compiling. Configure `TFT_eSPI` for your display, wiring, driver, and pin assignments.

### Pin assignments in the source

| Function | GPIO |
|---|---:|
| OK / Up button | 0 |
| Down / Back button | 14 |

Display wiring and driver configuration are handled by your `TFT_eSPI` setup and are not fully specified in this sketch.

## Configuration before flashing

Open `ysf_sabir.ino` and review the configuration near the top of the file:

1. `WIFI_SSID` and `WIFI_PASSWORD` — Wi-Fi credentials used for time synchronization.
2. `GMAIL_TOTP_BASE32` — the Base32 secret used to generate the configured TOTP.
3. `GMT_OFFSET_SEC` — currently set to UTC+3 in the source. Set this to the timezone you actually need.
4. `BTN_OK_UP` and `BTN_DOWN` — adjust these if your button wiring differs.
5. `TFT_eSPI` configuration — set the correct display driver, dimensions, pins, and SPI settings.

**Do not commit real Wi-Fi passwords, TOTP seeds, or personal account credentials to a public repository.** Use placeholders in shared copies and rotate any secret that has been exposed.

## Build and upload

1. Install the Arduino IDE or use PlatformIO with a compatible ESP32 board platform.
2. Install the libraries listed above.
3. Select the correct ESP32 board and USB mode/options required for USB HID.
4. Configure `TFT_eSPI` for your display hardware.
5. Update the sketch configuration and remove all example credentials.
6. Compile, then upload the sketch to the board.
7. Open the serial monitor only if your board setup or debugging workflow requires it; this sketch does not currently define serial logging.

Exact board-menu options vary by ESP32 model and Arduino core release.

## Controls

The sketch implements the following button behavior:

- Short press of the OK / Up button: move down through a menu.
- Hold the OK / Up button: activate the selected item or send the selected value through USB HID.
- Short press of the Down button: move up through a menu.
- Hold the Down button: go back / close the active screen or settings portal.

The button labels and navigation direction may feel counterintuitive; check the actual wiring and behavior on your device.

## Wi-Fi settings portal

When Settings is selected, the firmware starts a Wi-Fi access point. The access-point name is configured in the sketch and the password is generated at runtime. The TFT displays the portal instructions. Connect to that network and open a browser to manage saved entries.

The portal handles password data over a local HTTP server. **It does not provide HTTPS or user authentication in the current source.** Use it only in a trusted environment, do not expose it to an untrusted network, and do not store high-value credentials until the portal and storage design have been security-reviewed.

## Security notes

This project handles highly sensitive information. Before real-world use:

- Replace all sample usernames and passwords.
- Replace the example TOTP seed with your own secret, and never publish it.
- Avoid sharing screenshots or logs that reveal credentials or one-time codes.
- Review the local web portal for authentication, transport security, CSRF protection, and input validation.
- Consider encrypting stored credentials and protecting access to the device.
- Understand that USB HID sends text to whichever field or application currently has focus.
- Back up important credentials securely before testing firmware changes.

The current sketch stores password values using ESP32 `Preferences`; the source does not implement application-level encryption for those values.

## Troubleshooting

- **Display is blank or incorrect:** check the `TFT_eSPI` setup, display driver, dimensions, SPI pins, and rotation.
- **USB keyboard output does not work:** verify that the selected board supports native USB HID and that the board/core USB settings are correct.
- **TOTP initialization fails:** check that the Base32 secret contains a valid, non-empty secret and that the `TOTP` library matches the API used by the sketch.
- **TOTP time is incorrect:** verify Wi-Fi credentials, NTP access, and `GMT_OFFSET_SEC`.
- **Settings access point does not start:** check the board's Wi-Fi support and the generated AP password requirements.

## License and attribution

No license or original-author attribution was provided with the source file. Before redistributing this project, confirm that you have the right to modify and publish the original code, preserve any required notices, and add the applicable license and attribution here. A project rename does not change the copyright or license terms of upstream code.
