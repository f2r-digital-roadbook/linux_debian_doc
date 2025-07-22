# Y1000 Linux debian support package
This repository provides essential documentation for using **Armbian-unofficial 25.05.0 F2r-y1000 bookworm.**

## Update to last version
```bash
curl -O --output-dir /tmp https://raw.githubusercontent.com/f2r-digital-roadbook/linux_debian_doc/refs/heads/main/gamma1000d_1.0.4_arm64.deb
sudo apt install /tmp/gamma1000d_1.0.4_arm64.deb

```

## Change log
| Version | Changes | Date
|---------|--------|--------
|gamma1000d_1.0.4         |  Python API, minor fixes  | 2025/07/21
|gamma1000d_1.0.2         |  Fixes:      | 2025/05/19
| |- Sometimes the screen doesn't wake up from sleep
| |- After 10 minutes, even in motion the screen goes black |
|gamma1000d_1.0.1         |  Fix - Screen backlight low until rotate the brightness knob    |
|gamma1000d_1.0.0         |  Initial release    |

## Linux default user password 

- User: `*****`
- Pass: `*****`

- Root Password: `*****`


> [!WARNING] > Security Notice: It is highly recommended to change the default passwords after the initial setup to enhance system security.

## Generating armbian image

Instructions for building and flashing the Armbian image will be available soon.

## Customizations of armbian for F2R Y000
 
- The default screen rotation is set to portrait, see (`/boot/armbianEnv.txt`)
- Integrated functionalities:
    - The `gamma1000d_1.0.4_arm64.deb` installed by default.
    - Power button supports shutdown and startup
    - Dimmer push button:
        - **Long press:** Activates screen standby
        - **Short press:** Locks/unlocks the touch panel
    - Home and remote buttons mapped to Linux keys or commands

# Debian package Installation

## Installation of `gamma1000d_1.0.4_arm64.deb`


Installing `gamma1000d_1.0.4_arm64.deb`

To install the Debian package, use the following command:
```bash
sudo apt install -y ./gamma1000d_1.0.4_arm64.deb
```
## System Modifications Made by This Package

- Installs and enables gamma1000d as a systemd service by default
- Adds an initialization script to `/etc/lightgdm/display_setup.sh` and configuration file `20-gamma1000.conf` to configure portrait mode.
- Configuration files added to `/etc/gamma1000d/` with example configurations included

## `gamma1000d` configurations

This user-space service connects Linux with the device motherboard functionalities. Actions can be mapped to Linux commands or keyboard keys.

### Button Categories:

1. **Intents:** Automatically trigger keypress/release events when detected and mapped to keys.
2. **Pure Keys:** Regular keyboard key presses.

###  List of the intents and mapped scripts

>Default mappings are found in  `/etc/gamma1000d/gamma1000d.conf`

| Intent / key | Description  | Script/Key | 
|--------------|--------------|------------|
| Power        | Long press turn on/off the device| `power_button` |
| Dimmer       | Adjust display brightness| *None*
| Dimmer Push (Short)| Lock/unlock touch panel | `touchlock_toggle`
| Dimmer Push (Long) | Activate screen standby (Power-saving) | `standby_toggle`
| Remote Connected/Disconnect  | Triggered when a remote connects; sends these status codes: 0-Disconnected, 2-Motorbike, 3-Auto. | `dr_status`

**Power Status Detection:** Use the  `power_status` command to detect if the device is shutting down.
>All the scripts are located in 
```
/etc/gamma1000d/scripts/
```

### Home button and remote default key mappings.
| Intent / key | Description  | Key | 
|--------------|--------------|------------|
| Home         | Home button | `KEY_0` |
| Button 1     | ODO - | `KEY_1` |
| Button 2     | ODO + | `KEY_2` |
| Button 3     | Check | `KEY_3` |
| Toggle push  | Roadbook Forward | `KEY_4` |
| Toggle pull  | Roadbook Rewind| `KEY_5` |



# Device Power Management

The device features an integrated power management system designed to minimize vehicle battery drain. It operates across 3 power states, triggered by either the internal accelerometer or GNSS module detecting inactivity. Future versions will incorporate battery charge status detection.

### Power States and Timers
| State Name | Description | Timer | Power consumption |
| ---------- | ----------- | ----- | ----------------  |
| Full Power |Active while the device is in motion, 100% Brightness| N/A | ~1500mA/h @ 13V |
| Full Power |Active while the device is in motion, 60% Brightness| N/A | ~600mA/h @ 13V |
| Brightness Dimmed | Screen brightness reduced to 20% | 5 min | 300mA/h @ 13V
| Screen Standby | Screen off | 30 min | 200mA/h @ 13V
| Shutdown | Device is turned off | 2 hours | < 100μA/h @ 13V

>All timers start when a steady state is detected.

