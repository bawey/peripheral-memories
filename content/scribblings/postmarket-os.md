---
title: "Postmarket OS"
date: 2023-12-17T14:18:39+01:00
draft: false
tags: [android, linux, "postmarket os"]
---

[usb_internet_setup]: https://wiki.postmarketos.org/wiki/USB_Internet
[pmbootstrap]: https://wiki.postmarketos.org/wiki/Pmbootstrap

[Postmarket OS](https://postmarketos.org) is an OS project that allows flashing an obsolete android device with a Linux
OS capable of giving it a new life as a web server or something similar.

## Giving it a go
Your mileage will likely vary depending on how well supported your devices are. You can find out [here](https://wiki.postmarketos.org/wiki/Devices).

### Fully supported devices
To be seen, probably with an emulator.

### Partially supported devices (testing category)
⚠ [`pmbootstrap`][pmbootstrap] will be required to proceed.
#### [Samsung Galaxy Tab 3 8.0](https://wiki.postmarketos.org/wiki/Samsung_Galaxy_Tab_3_8.0_(SM-T310)_(samsung-lt01wifi)#Installation)
##### Installation steps
- `pmbootstrap init` and answer the prompts:
  - `work path`: default (e.g., `/home/$USER}/.local/var/pmbootstrap`)
  - `channel`: `edge`
  - `vendor`: `samsung`
  - `device codename`: `lt01wifi`
  - `username`: whatever you'd like to log in as
  - `user interface`: splashscreen won't go away no matter what, pick `none` or `console`
  - `additional packages`: doesn't matter
  - ...
- `pmbootstrap status` (optional if re-building over the days/weeks that follow?)
- `pmbootstrap install`
  - you will be asked for the new user`s password
- `pmbootstrap flasher flash_rootfs` (use power + volume down to reboot into recovery)
- `pmbootstrap flasher flash_kernel` (the device will reboot after the previous step so head back to recovery first)

##### Using the device
What we get is a splashscreen that doesn't go away but the device boots and can be `ssh`ed into over USB:
- `172.16.42.1` will be the device IP, barring exceptional cases
  - You can find the address of the newly added interface with `ip addr` (typically `172.16.42.2`) 
- `ssh user@172.16.42.1` (username specified during install)

Alas, the onboard WiFi would only go as far as to scan for and find the available networks. 
Any attempt to connect ends up in `device cannot be readied` or something similar. 
Fortunately, the [USB][usb_internet_setup] can be used instead for playing around and what not.

## Useful commands
| Purpose                         | Command                               |
|---------------------------------|---------------------------------------|
| see the logs                    | `logread`                             |
| list services                   | `rc-update`                           |
| interact with a service         | `sudo rc-service $SERVICE restart`    |
| remove a service from autostart | `sudo rc-update del $SERVICE default` |

## Useful links
- [pmbootstrap guide][pmbootstrap]
- [Porting to a new device](https://wiki.postmarketos.org/wiki/Porting_to_a_new_device)
  - [Finding device-specific info](https://wiki.postmarketos.org/wiki/How_to_find_device-specific_information#Kernel_defconfig_(default_config))
- [Terminal cheatsheet](https://postmarketos.org/cheatsheet)
- [WiFi troubleshooting](https://wiki.postmarketos.org/wiki/WiFi#WiFi_doesn't_auto_connect)
  - [USB internet setup][usb_internet_setup]




