# Wired headset not auto-switching audio output on Ubuntu

Plugging a wired headset into the PC's front-panel audio jack did not automatically switch the system's audio output. The output stayed on the monitor's HDMI audio, and the headset had to be selected manually in Settings every time, even though the OS visibly detected the jack.

# Device details
- Host: NbDE-WXX9 (M1010)
- OS: Ubuntu 25.10 x86_64
- Kernel: Linux 6.17.0-41-generic
- DE / WM: GNOME 49.0, Mutter (Wayland)
- CPU: 11th Gen Intel Core i7-1195G7 (8) @ 5.00 GHz
- GPU: Intel Iris Xe Graphics (Integrated)
- Audio chip: Conexant SN6140 (HDA Intel PCH, snd_hda_intel driver)
- Audio manager: PipeWire 1.4.7 (with pipewire-pulse compatibility layer), WirePlumber as session manager
- Displays: Built-in 14" (BOE0877, 1920x1080 @ 60Hz), External 24" BenQ (1920x1080 @ 120Hz)
- Output devices involved: External BenQ monitor via HDMI audio, wired headset via motherboard 3.5mm jack, Bluetooth earbuds (unaffected, worked fine)

# What is the problem
ALSA correctly detected the headphone jack state (`analog-output-headphones` port flipped from "not available" to "available" on plug-in, confirmed via `pactl list cards`). However, the sound card's active profile (`output:hdmi-stereo+input:analog-stereo`) does not include any analog output port, so there was no route for the system to switch to even though the hardware event fired correctly. The card's `api.acp.auto-port` and `api.acp.auto-profile` properties — which control whether PipeWire is allowed to automatically switch the active profile in response to a port becoming available — were both set to `false`, blocking any automatic profile change regardless of jack detection.

# Fix
Re-enabled automatic profile/port switching for the built-in audio card via a WirePlumber user configuration override, then restarted the audio services to apply it.

- Ran `pactl info`, `pactl list short sinks`, and opened `pavucontrol` to confirm devices were detected and manual switching worked, ruling out a hardware fault
- Used `wpctl status` and `journalctl --user -u wireplumber -f` (live log while plugging/unplugging) to confirm no WirePlumber session event fired at all for the wired jack
- Ran `pactl list cards` unplugged vs. plugged and diffed the output — confirmed ALSA jack sensing worked, but the active profile had no analog output port, and found `api.acp.auto-port = false` / `api.acp.auto-profile = false` on the card
- Created `~/.config/wireplumber/wireplumber.conf.d/51-alsa-auto-profile.conf` with a `monitor.alsa.rules` match on the card, setting both properties to `true`
- Restarted the audio stack: `systemctl --user restart wireplumber pipewire pipewire-pulse`
- Verified by unplugging (falls back to HDMI monitor audio) and replugging (switches to headphones automatically) — confirmed working, no manual Settings change needed
