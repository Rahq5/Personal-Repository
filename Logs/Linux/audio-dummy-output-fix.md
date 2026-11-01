## Problem Title
Laptop audio not working on both Ubuntu and Fedora — all outputs listed as "Dummy Output"

**Describe the problem:**
On both Ubuntu and Fedora installed on the same laptop, no sound output worked. In the system's audio settings, every available output device was shown as "Dummy Output" instead of the real speakers/headphones. This is a symptom of the kernel loading the generic HDA Intel audio driver instead of the correct SOF (Sound Open Firmware) driver that this specific Intel audio hardware requires.

## Fix
The fix is a kernel boot parameter, added to the `GRUB_CMDLINE_LINUX_DEFAULT` line in `/etc/default/grub`, forcing the kernel to use the SOF driver instead of auto-detecting.

**What was written in the file (edited with `sudo nano /etc/default/grub`):**
```
GRUB_CMDLINE_LINUX_DEFAULT="snd-intel-dspcfg.dsp_driver=1"
```
(Note: `quiet splash` was intentionally excluded to isolate the audio fix as the only change.)

**Command to apply the change after editing:**
- Ubuntu:
```
sudo update-grub
```
- Fedora:
```
sudo grub2-mkconfig -o /boot/efi/EFI/fedora/grub.cfg
```
(or `/boot/grub2/grub.cfg` if BIOS/legacy boot)

Then reboot for the parameter to take effect.

## Why Other Fixes May Not Work
This issue is caused deep inside the Linux boot process — specifically, which kernel audio driver gets loaded at boot time. Because of that, no user-space or third-party audio tool can fix it, since those tools only manage audio *after* the kernel has already loaded the wrong driver. This includes tools like:
- **alsamixer** — only adjusts volume levels/mixer channels for whatever driver is already loaded; it cannot load a different driver.
- **pavucontrol** (PulseAudio Volume Control) — only manages audio streams and routing between already-detected devices; it cannot detect hardware the kernel driver never exposed.

Since the real hardware was never properly exposed to the OS in the first place (masked by the generic HDA driver), no amount of configuration in these tools could reveal the actual speaker/headphone hardware. The only real fix is forcing the correct driver at the kernel/boot level, which is what the `snd-intel-dspcfg.dsp_driver=1` parameter does.
