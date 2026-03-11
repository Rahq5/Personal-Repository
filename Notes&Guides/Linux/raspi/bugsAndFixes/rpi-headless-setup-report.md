# Raspberry Pi 5 Headless Setup — Troubleshooting Report

---

## Introduction

A **headless Raspberry Pi** is a Pi that runs without a monitor, keyboard, or mouse — you interact with it entirely through SSH over the network. The goal of this setup was to have the Pi 5 boot automatically, connect to WiFi silently, and be permanently reachable via SSH without any manual intervention.

---

## Problem Scenario

1. Flashed Raspberry Pi OS (Full, 64-bit) using Raspberry Pi Imager with WiFi credentials and SSH pre-configured
2. Pi booted and appeared on the network via `nmap -sn 192.168.8.0/24`
3. SSH worked on first session
4. After rebooting the Pi, it disappeared from the network
5. Plugging in a monitor revealed a popup:
   > *"Authentication required by WiFi network — passwords or encryption keys are required to access the WiFi network 'stc_wifi_8145'"*
6. Clicking **Connect** on the popup restored the network — SSH started working again
7. While connected via SSH and the popup appeared in the background, **all SSH input froze** until the popup was clicked on the monitor
8. The Pi also kept receiving a different IP address on every reboot, making SSH unreliable

---

## Root Causes

### 1. GNOME Keyring blocking WiFi on boot
The full Raspberry Pi OS ships with a desktop environment (GNOME). GNOME Keyring is a password vault that stores WiFi credentials — but it only unlocks **after a user logs into a desktop session**. On a headless boot with no user session, the keyring never unlocks, so WiFi never connects automatically, and a popup appears demanding manual authentication.

### 2. Wrong WiFi password stored in NetworkManager
During attempted fixes, the wrong password (`ra***********` instead of `at********`)(astric for privacy) was stored via `nmcli`, causing the keyring to keep rejecting the credentials silently even after workarounds were applied.

### 3. Dynamic IP on every reboot
The router's DHCP server was assigning a random available IP to the Pi on each boot, making it impossible to SSH to a consistent address without running `nmap` every time.

---

## Failed Approaches

| Approach | Why it failed |
|---|---|
| `sudo raspi-config` → S1 Wireless LAN | Threw an error on password entry |
| `nmcli connection modify connection.permissions ""` | Correct command but wrong password was stored, so keyring still complained |
| Removing `gnome-keyring` via `apt remove` | Broke the desktop login session entirely, couldn't start a GUI session |
| Editing `/etc/rc.local` with `iwconfig wlan0 power off` | WiFi power saving wasn't the actual root cause |
| Disabling keyring autostart via `~/.config/autostart/` | Partially applied but wrong password negated the fix |
| Switching to Raspberry Pi OS Lite | WiFi stayed soft-blocked (`rfkill list` showed `Soft blocked: yes`) due to WiFi country not being set to `SA` in Imager |

---

## Final Fix

The combination of three things resolved the issue permanently:

### Step 1 — Store WiFi credentials at system level with correct password
```bash
sudo nmcli connection modify "stc_wifi_8145" connection.permissions ""
sudo nmcli connection modify "stc_wifi_8145" connection.autoconnect yes
sudo nmcli connection modify "stc_wifi_8145" wifi-sec.psk "atEAaFBmFR8"
```

- `connection.permissions ""` — makes the connection available system-wide, not tied to a user session
- `connection.autoconnect yes` — connects on boot automatically
- `wifi-sec.psk` — stores the **correct** WiFi password at system level, bypassing keyring

### Step 2 — Disable GNOME Keyring autostart
```bash
mkdir -p ~/.config/autostart
cp /etc/xdg/autostart/gnome-keyring-secrets.desktop ~/.config/autostart/
echo "X-GNOME-Autostart-enabled=false" >> ~/.config/autostart/gnome-keyring-secrets.desktop
```

This stops GNOME Keyring from launching on boot — no keyring, no popup, no freeze.

### Step 3 — Fix IP via DHCP reservation on router
1. Get Pi's WiFi MAC address:
```bash
ip link show wlan0
```
2. Log into router at `192.168.8.1`
3. Navigate to **Router → DHCP → IP and MAC Address Binding List → +**
4. Add Pi's MAC address with reserved IP `192.168.8.200`

---

## Current Stable Setup

| Property | Value |
|---|---|
| OS | Raspberry Pi OS Full 64-bit |
| Hostname | `raspberrypi` |
| Reserved IP | `192.168.8.200` |
| SSH command | `ssh rawi-raspberrypi@192.168.8.200` |
| WiFi | Connects silently on boot, no popup |
| Router | Huawei H155-381 (STC 5G CPE) |

---

## Lesson Learned

The root cause was always the **GNOME Keyring + wrong password combination**. Every other symptom (freeze, disappearing from network, popup) was a consequence of those two things. Switching to **Raspberry Pi OS Lite** would have been the cleanest solution from the start — no desktop means no keyring means no problem. For any future headless Pi setup, Lite OS is the recommended choice.

---

*Report written: March 2026*
