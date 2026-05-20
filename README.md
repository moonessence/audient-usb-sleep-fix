# audient-usb-sleep-fix
Fix for Audient iD audio interfaces audio drop/disconnect issues after system sleep (systemd-sleep script for Linux).


# Audient iD USB Audio Interface Sleep Fix for Linux

This script resolves the common issue where Audient audio interfaces (such as iD4, iD14, and other revisions with Vendor ID `2708`) fail to initialize, produce loud clicks, or completely drop from the USB bus after the Linux system wakes up from suspend/sleep states.

## How It Works
The script hooks into the native `systemd-sleep` environment:
* **Before suspend (`pre`):** It cleanly unbinds the ALSA `snd-usb-audio` driver from the device and toggles the USB port power state, preventing kernel endpoint and descriptor corruption.
* **After wake up (`post`):** It runs a safe, pure POSIX polling loop to wait for the USB subsystem to settle, forces a driver re-bind, and completely disables aggressive runtime power management (`power/control = on`) to stabilize the live audio stream.

## Installation

1. Create the hook script in your systemd directory:
   ```bash
   sudo nano /usr/lib/systemd/system-sleep/audient-usb

Copy the contents of the audient-usb script from this repository into the file.

    Make the script executable:
    Bash

    sudo chmod +x /usr/lib/systemd/system-sleep/audient-usb

Manual Testing & Diagnostics

You can manually simulate system sleep triggers to verify execution without putting your PC into a full suspend cycle.

Simulate Suspend (Disconnect & Isolate):
Bash

sudo /usr/lib/systemd/system-sleep/audient-usb pre

Simulate Resume (Reconnect & Re-bind):
Bash

sudo /usr/lib/systemd/system-sleep/audient-usb post

Monitor execution logs in real-time:
Bash

sudo journalctl -f -n 50

Credits

    Developed and maintained by moonessence.

    Refined, optimized for strict POSIX compliance, and audited with the help of Google Gemini Flash AI.

License

MIT License. Feel free to modify, fork, and distribute.
