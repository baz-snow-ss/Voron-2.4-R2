## Mainboard flashing BTT Manta M8P with CB1 v1.1

Enter DFU Mode on the mainboard  `Boot` `Reset`
```
cd ~/CanBoot
make menuconfig
```
### CanBOOT Config (note this just uses the default USB communication interface)
![image png ad03682ba08fc79626c8fd0432e13b16](https://github.com/baz-snow-ss/Voron-2.4-R2/assets/99566898/7417519c-cc2b-4d70-a39b-7f7fe73464d4)
```
make clean
make
dfu-util -l
```
![image thumb png afcb3c1f386455718f5aecbdece78502](https://github.com/baz-snow-ss/Voron-2.4-R2/assets/99566898/cfdc2fd5-1c1d-450b-8bd8-2a0d7e195099)
```
dfu-util -R -a 0 -s 0x08000000:force:mass-erase:leave -D ~/CanBoot/out/canboot.bin -d 0483:df11
```
![image thumb png 9353a8be076226ee599728a395a32864](https://github.com/baz-snow-ss/Voron-2.4-R2/assets/99566898/84ff53c3-c6f2-4fe0-94d4-2c4c0c1fae29)
CanBoot is Now flashed, Flashing klipper via CanBOOT will be covered shortly.
```
cd ~/klipper/
make menuconfig
```
### Klipper Config uses USB-CAN-Bridge
![image png b60a7a91ac433f5bbc54ac4b7e1b8446](https://github.com/baz-snow-ss/Voron-2.4-R2/assets/99566898/61f0cb8f-38d6-43cc-902e-fdc24d14ca61)
```
make clean
make
```
### If you have CanBOOT installed
```
ls /dev/serial/by-id/
```
![image png 50cd3f002a25048e9cd409c0bdd5f51c](https://github.com/baz-snow-ss/Voron-2.4-R2/assets/99566898/90e5ea8c-05b8-417d-83bc-6e329dbb83e7)

Run this command to install klipper firmware via canboot via USB. Use the device ID you just retrieved in the above ls command.
```
python3 ~/CanBoot/scripts/flash_can.py -f ~/klipper/out/klipper.bin -d /dev/serial/by-id/usb-CanBoot_stm32g0b1xx_52002D000D504B4633373520-if00
```
![image thumb png cdf77b51d83df188a25103018e2e527f](https://github.com/baz-snow-ss/Voron-2.4-R2/assets/99566898/60e09b27-806c-4ca6-b825-fd8a900a825b)

### If you are running a stock bootloader and flashing via SD card INSTEAD of CanBOOT
Simply follow the mainboard user manual to copy the ~/klipper/out/klipper.bin file to an SD card (renaming it if needed) and flash the mainboard as per user manual.
### If you are flashing via DFU mode (no CanBOOT or stock bootloader)
Then simply run the following commands to change to the klipper directory then flash the mainboard.
```
cd ~/klipper
make flash FLASH_DEVICE=0483:df11
```
where the FLASH_DEVICE ID is the address of the USB device
### Klipper is now installed

This should have now installed klipper firmware to your mainboard. You can verify by running lsusb and you should see a "Geschwister Schneider CAN adapter" or similar device.
![image png 7aa5df0bf8b259d6d7d17d17c0bb6ccf](https://github.com/baz-snow-ss/Voron-2.4-R2/assets/99566898/78b2cde7-5be8-447a-9384-7357710e6886)
You can also check by running an 'interface config' command ifconfig. If the USB-CAN-Bridge klipper is up and happy (and you have created the can0 file mentioned in the main page) then you will see a can0 interface:
![image png 3a2ff9829ff81d4d63392728769c8eb1](https://github.com/baz-snow-ss/Voron-2.4-R2/assets/99566898/3f5c9a1d-b53e-4363-a955-388215334f5c)
You can now run the Klipper canbus query to retrieve the canbus_uuid of your mainboard:
```
~/klippy-env/bin/python ~/klipper/scripts/canbus_query.py can0
```![image png 418bd590f41553179dfd4d9f2fc2c3f9](https://github.com/baz-snow-ss/Voron-2.4-R2/assets/99566898/a7e0378a-dfdd-43f2-b9f6-282c3301bec0)

Use this UUID in the [mcu] section of your printer.cfg in order for Klipper (on Pi) to connect to the mainboard.

