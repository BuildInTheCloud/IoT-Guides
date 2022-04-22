# Guides for IoT Stuff

## Common Raspberry Tasks
1. `sudo raspi-config` is used in SSH shell to make core setting changes like network settings and enable VNC.
2. `sudo shutdown -h now` SSH safe shutdown of Pi
3. `sudo reboot` reboot Pi
4. Slow mouse cursor. `sudo nano /boot/cmdline.txt` add `usbhid.mousepoll=0`

## Common Arduino Tasks

## Setup Guides
1. [Using Pi as dev box](./dev-setup.md)
2. [How to setup RDP](./guides/RDP.md)
3. [HackRf One](./guides/HackRf-One.md)
4. [LoRa](./guides/LoRa.md)
5. [MQTT](./guides/MQTT.md)
6. [OpenRazer](https://openrazer.github.io/)
7. [Ansible](./guides/ansible.md)

## Boards
1. [Waveshare PN532-NFC-Hat](./boards/waveshare-pn532-nfc-hat.md)
2. [Heltec](./boards/heltec.md)

## Builds
1. [piAP](./projects/piAP.md)
2. [piDev](./projects/piDev.md)
3. [piTouch](./projects/piTouch.md)
4. [piTouchMini](./projects/piTouchMini.md)

## iOS from Windows
1. [Using nvm on Windows](https://www.damirscorner.com/blog/posts/20211126-UsingNvmOnWindows.html)