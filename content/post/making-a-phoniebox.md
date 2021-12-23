---
title: "Making a Phoniebox"
date: 2021-12-23T11:35:06-05:00
---

A [Phoniebox](http://phoniebox.de/index-en.html) is a music Jukebox that plays songs when RFID cards are swiped near it.

# References
- [Phoniebox installation log · GitHub](https://gist.github.com/marians/95997ae0d83eef688ed9ff5c9cf44b6b)
- [[Tutorial] Phoniebox](https://koboldimkopf.wordpress.com/2020/01/10/tutorial-phoniebox/)
- [Reddit - Finished MusicBox](https://www.reddit.com/r/RASPBERRY_PI_PROJECTS/comments/9v7h4x/finished_rfid_musicbox_for_the_kids_based_on/)
- [Reddit - RFID Music Box V3 Phoniebox](https://www.reddit.com/r/RASPBERRY_PI_PROJECTS/comments/as2pqh/finished_rfid_music_box_v3_phoniebox/)
- [GitHub - MiczFlor/RPi-Jukebox-RFID](https://github.com/MiczFlor/RPi-Jukebox-RFID)
- [Installing Phoniebox future3 — RPi Jukebox RFID 3.1.0 documentation](https://rpi-jukebox-rfid.readthedocs.io/en/latest/install.html)

# Installation

## 1. Download Raspbian Buster Lite

from https://www.raspberrypi.org/downloads/raspbian/ , using the ZIP download link.

## 2. Flash an SD card

1. Use Etcher to flash SD Card with Buster image
1. Take out SD card, put it back in

## 3. Modify Wifi settings

1. Open SD card partition "boot"
1. Create file `ssh`
1. Create file `wpa_supplicant.conf` as described in https://www.raspberrypi-spy.co.uk/2017/04/manually-setting-up-pi-wifi-using-wpa_supplicant-conf/

## 4. Boot the Raspi

1. Insert the card
2. Plug in the card reader
3. Plug in the USB audio device (optional)
4. Plugin monitor and keyboard (optional)

## 5. Connect via SSH & Bootstrap Machine

1. Find IP address via router admin UI
1. Connect: `ssh pi@<ip-address>`
1. Create `.ssh/authorized_keys` and add own public RSA key
1. Change password for pi user using `passwd`
1. Setup private key access
1. Disable Password authentication for ssh in `/etc/sshd_config`
1. [Set Hostname](https://www.tomshardware.com/how-to/raspberry-pi-change-hostname)


## 7. Get sound device info
1. Setup DT Overlay for HiFiBerry MiniAmp
```
#/boot/config.txt
dtoverlay=hifiberry-dac
```

```
#/etc/asound.conf
pcm.hifiberry {
    type softvol
    slave.pcm "plughw:0"
    control.name "Master"
    control.card 0
}
pcm.!default {
    type plug
    slave.pcm "hifiberry"
}
```
2. Reboot


Use this info in the next step.

## 6. Start Phoniebox install script

From https://github.com/MiczFlor/RPi-Jukebox-RFID/wiki/INSTALL-stretch#one-line-install-command

```
rm buster-install-*
wget https://raw.githubusercontent.com/MiczFlor/RPi-Jukebox-RFID/master/scripts/installscripts/buster-install-default.sh
chmod +x buster-install-default.sh
./buster-install-default.sh
```

Follow the interactive prompt.

If you are connected via Wifi already, skip the Wifi setup.

WiFi -> n

PCM as iFace -> no, should be „Master“ (if you use a HiFiBerry MiniAmp)

mpd -> y

default locations -> y

Start the first part of the installation with another y and wait some time.

## 7. Connect to the web UI

1. Use `ifconfig | grep inet` to find the Raspi's IP address.
2. In a browser, connect to `http://<ip-address>/` to open the Phoniebox web UI.

## 8. Upload music

1. Have some MP3 files ready
2. Go to "Folders & Files" to upload music files into new folders


## 9. Test audio playback

1. maybe disable `mpd` if it doesn't work right?
```
sudo systemctl disable mpd
```