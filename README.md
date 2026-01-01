# Updated repo : [Click Here](https://github.com/EmberHeart-Kernels/EmberHeart_OnePlus11)

![Banner](svgs/emberheart_light.svg#gh-light-mode-only)
![Banner](svgs/emberheart_dark.svg#gh-dark-mode-only)

---
[Guide to fix Unusually long boot times on A16 when using Nethunter](/a16_fix.md)
---

### Your warranty is no longer valid!

I am not responsible for bricked devices, dead SD cards, thermonuclear war, or the current economic crisis. Please do some research if you have any concerns about features included in this kernel before flashing it! YOU are choosing to make these modifications, and if you point your finger at me for messing up your device, I will laugh at you.

---

## Other Links:

[Kernel Flasher - fatalcoder524 fork](https://github.com/fatalcoder524/KernelFlasher)
---

## Installation instructions: 

- Flash AK3 zip in kernel flasher
- Flash Wireless Firmware for Nethunter provided in the releases 
- Download and Unzip kernel modules in internal storage and load them using `insmod module_name.ko`

---

## Loading rtw88 drivers

If you have unzipped all the drivers in internal storage and want to load drivers for let's say rtl8821au chipset,

- Step 0: cd into the directory where all kernel modules are unzipped 
- Step 1: `rmmod mac80211`
- Step 2: `insmod mac80211.ko`

> [!WARNING] 
> The first two steps are necessary otherwise you'll get unknown symbol error (__ieee80211_create_tpt_led_trigger) 

- Step 2: `insmod rtw_core.ko`
- Step 3: `insmod rtw_usb.ko`
- Step 4: `insmod rtw_88xxa.ko`
- Step 5: `insmod rtw_8821a.ko`
- Step 6: `insmod rtw_8821au.ko`

Tested wifi adaptors : [TP-Link Archer T2U Plus](https://amzn.in/d/76Ka5nB)

---

## Features

- **KernelSU**: KernelSU is a root solution for Android GKI devices, it works in kernel mode and grants root permission to userspace applications directly in kernel space.
- **SUSFS**: An addon root hiding kernel patches and userspace module for KernelSU.
- **Nethunter**: Open-source Android penetration testing platform for Android devices,
---

## Credits

- **KernelSU**: Developed by [tiann](https://github.com/tiann/KernelSU).
- **KernelSU-Next**: Developed by [rifsxd](https://github.com/KernelSU-Next/KernelSU-Next).
- **Magic-KSU**: Developed by [5ec1cff](https://github.com/5ec1cff/KernelSU).  
- **SUSFS**: Developed by [simonpunk](https://gitlab.com/simonpunk/susfs4ksu.git).
- **SUSFS Module**: Developed by [sidex15](https://github.com/sidex15).
- **Sultan Kernels**: Developed by [kerneltoast](https://github.com/kerneltoast).
- **Kernel Flasher**: Developed by [fatalcoder524](https://github.com/fatalcoder524)

Special thanks to the open-source community for their contributions!

---

## Support

If you encounter any issues or need help, feel free to open an issue in this repository or reach out to me.

---

## Disclaimer

Flashing this kernel will void your warranty, and there is always a risk of bricking your device. Please make sure to back up your data and ensure you understand the risks before proceeding.

**Proceed at your own risk!**

---

If you have contributed and are not here please remind me!
