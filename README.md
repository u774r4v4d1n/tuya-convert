﻿
# TUYA-CONVERT

A Chinese company named Tuya offers a free-to-brand turnkey smart home solution to anyone. Using their offer is dead-simple, since everything can be done by clicking through the [Tuya web page](https://en.tuya.com/), from choosing your pre-designed products or pre-programmed wifi-modules (mostly ESP8266) to building your own app. In the end, this has resulted in as they claim over 11 000 devices 'made' by over 10 000 vendors using Tuyas firmware and cloud services.

Aside from that, they claim their cloud solution has 'military grade security'. Michael Steigerwald, founder of the German IT security startup VTRUST, was able to disprove this claim and presented his results in the "Smart home - Smart hack" talk at 35C3 in Leipzig: https://media.ccc.de/v/35c3-9723-smart_home_-_smart_hack

In the following days, VTRUST and the German tech magazine c't decided to work together. Since reflashing devices using the ESP8266/85 is widespread among DIY smart home enthusiasts, we wanted to provide an easy way for everyone to free their devices from the cloud without the need for a soldering iron. 

Please make sure to visit VTRUST (https://www.vtrust.de/), since the hack is their work.

## 🚨WARNING🚨
Please be sure that you understand what you're doing before using this software. Flashing an alternative firmware can lead to unexpected behavior and/or render the device unusable, so that it might be permanently damaged (highly unlikely) or require soldering a serial connection to the processor in order to reflash it (likely). 

### ⚠️Be aware that you use this software at your own risk so neither VTRUST nor c't/heise can be held accountable for any damage done or loss of functionality⚠️. 

TUYA-CONVERT only provides with the means to backup the original and flash an alternative firmware. Please do not ask for hardware support for your favorite alternative firmware in this repository, rather open an issue in the corresponding repository.

## DOCUMENTATION
Since Tuya devices are spread around the world with likely a vast amount of different brand names, please tell the community if you find one! There is a device list in the wiki that you can help extend. Please at least add the device model number, brand name, geographical area where you have bought the device and its flash mode (as seen in the device information). Add the GPIO assignments as well if you have found them to save the developers of alternative firmwares some time. Please note that we do not develop or maintain alternative firmwares and so post installation issues should be directed to the respective project.

[![asciicast](https://asciinema.org/a/2aDZweVGfliwc9TjB1ncwmKvm.png)](https://asciinema.org/a/2aDZweVGfliwc9TjB1ncwmKvm
)

## REQUIREMENTS
* Linux computer with a wifi adapter
* Secondary wifi device (e.g. smartphone)
* Dependencies (will be installed by install_prereq): python3, dnsmasq, hostapd, screen, curl, python3-pip, python3-setuptools, python3-wheel, mosquitto, paho-mqtt, pyaes, tornado

These scripts were tested in 
* Kali-Linux 2018.4 in VMWARE
* a Raspberry Pi 3B / 3B+ with Raspbian Stretch and its internal Wifi chip
* a Raspberry Pi 3B+ + USB-WIFI with this image from [here](https://www.offensive-security.com/kali-linux-arm-images/)
	https://images.offensive-security.com/arm-images/kali-linux-2018.4a-rpi3-nexmon-64.img.xz
	
Any Linux with a Wifi adapter which can act as an Access Point should also work. Please note that we have tested the Raspberry Pi with clean installations only. If you use your Raspberry Pi for anything else, we recommend using another SD card with a clean installation.

## PROCEDURE

As of January 28th, 2019, Tuya has started [distributing a patch](https://www.heise.de/newsticker/meldung/Smart-Home-Hack-Tuya-veroeffentlicht-Sicherheitsupdate-4292028.html) that prevents tuya-convert from completing successfully. It is up to the individual brands to adopt the patch, so some devices may be affected sooner than others. To ensure the best chance of success, **do not connect your device with the official app** as it may automatically update the device, preventing you from flashing with tuya-convert. Some devices are already being shipped with the update, in which case there is unfortunately no work around available at this time.

### INSTALLATION
    # git clone https://github.com/ct-Open-Source/tuya-convert
    # cd tuya-convert
    # ./install_prereq.sh
### flash loader firmware + backup
    # ./start_flash.sh

Follow the instructions in the start_flash script. It will install our flash loader onto the ESP and connect to the access point created by your wifi adapter.

    WIFI: vtrust-flash
    PASS: flashmeifyoucan
    IP: 10.42.42.42
A backup of the original firmware will be created and stored locally

### Device information
After the firmware backup procedure, the retrieved device information will be shown.
Please make sure to write down your devices flash mode and size!
You can show this information again by executing:

    # curl http://10.42.42.42
### BACKUP only and UNDO
You can use the flash loader to create a backup only.
If you want to delete the FLASH loader out of the flash again and go back to the stock software just do following:

    # curl http://10.42.42.42/undo
### FLASH loader to user2
The FLASH loader only allows flashing the third party firmware if the loader is running in the userspace user2 starting from 0x81000.
This will flash the FLASH loader in user2 if it is not already there.
It will destroy your ability to undo and go back to the original firmware

    # curl http://10.42.42.42/flash2

### FLASH third-party firmware
BE SURE THE FIRMWARE FITS YOUR DEVICE!
1. Place or link your binary file to ./files/thirdparty.bin.

	Currently a Sonoff-Tasmota [v6.5.0](https://github.com/arendst/Sonoff-Tasmota/releases/tag/v6.5.0) basic build is already included. It will open an WiFi access point named `sonoff-XXX`. Please note that while we include this for your convenience, we are not affliated with the Sonoff-Tasmota project and cannot provide support for post installation issues. Please refer to the respective project for support.
	
	Binary requirements:
	* full binary including first-stage bootloader
	* maximum filesize 512KB for first flash

3. Start flashing process

        # curl http://10.42.42.42/flash3

Alternatively you can request a certain file to be requested and flashed by the device:

	# curl http://10.42.42.42/flash3?url=http://10.42.42.1/files/thirdparty.bin

## RELATED WORKS
- [TuyAPI](https://github.com/codetheweb/tuyapi) NPM library for LAN control of Tuya devices with stock firmware
- [TuyOTA](https://github.com/SynAckFin/TuyOTA) Perl based Tuya flashing script using a similar strategy
- [MockTuyaCloud](https://github.com/kueblc/mocktuyacloud) Framework replicating much of the Tuya cloud functionality

