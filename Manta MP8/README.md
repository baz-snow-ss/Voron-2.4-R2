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
ls /dev/serial/by-id/
![image png 50cd3f002a25048e9cd409c0bdd5f51c](https://github.com/baz-snow-ss/Voron-2.4-R2/assets/99566898/90e5ea8c-05b8-417d-83bc-6e329dbb83e7)
Run this command to install klipper firmware via canboot via USB. Use the device ID you just retrieved in the above ls command.
```
python3 ~/CanBoot/scripts/flash_can.py -f ~/klipper/out/klipper.bin -d /dev/serial/by-id/usb-CanBoot_stm32g0b1xx_52002D000D504B4633373520-if00
```
![image thumb png cdf77b51d83df188a25103018e2e527f](https://github.com/baz-snow-ss/Voron-2.4-R2/assets/99566898/60e09b27-806c-4ca6-b825-fd8a900a825b)
