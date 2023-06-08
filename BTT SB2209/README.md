## Toolhead Flashing BTT SB2209 CanBus
<p> As mentioned in the main guide, you can either use CanBOOT on your toolhead to facilitate 
  flashing over CAN, or you can go without and have the board boot straight into klipper.</p>

### Installing CanBOOT

```
cd ~
git clone https://github.com/Arksine/CanBoot
```
To configure the CanBoot firmware, run these commands to change into the CanBoot directory and then modify the firmware menu:
```
cd ~/CanBoot
make menuconfig
```
You want the Processor, Clock Reference, and Application Start offset to be set as per whatever board you are running. Make sure "Communication Interface" is set to "CAN bus" with the appropriate pins for your board. Also make sure the "Support bootloader entry on rapid double click of reset button" is marked. It makes it so a double press of the reset button will force the board into CanBOOT mode. Makes re-flashing after a mistake a lot easier.

### CanBOOT Config

![Alt text](https://www.teamfdm.com/uploads/monthly_2023_05/image.png.07956cacf85e49a21a5cd36210999534.png)

```
make clean
make
```
To flash, connect your toolhead board to the Pi via USB then put the toolhead board into DFU/BOOT mode (your toolhead board user manual should have instructions on doing this).

STM32 based board:

To confirm it's in DFU mode you can run the command `dfu-util -l` and it will show any devices connected to your Pi in DFU mode or use `lsusb` .
```
dfu-util -R -a 0 -s 0x08000000:force:mass-erase:leave -D ~/CanBoot/out/canboot.bin -d 0483:df11
```
![Alt text](https://www.teamfdm.com/uploads/monthly_2023_05/image.png.8a9f1e4a1f6d1ddd6763989cd716c356.png)

### CanBOOT is now installed

CanBOOT should now be successfully flashed. Take your toolhead out of DFU mode (it might require removing jumpers and rebooting, or just rebooting).

Wire up your toolhead power (24v and gnd) and CAN (CANH/CANL) wires, then the following command to see if the toolhead board is on the CAN network and waiting in CanBOOT mode
```
python3 ~/CanBoot/scripts/flash_can.py -i can0 -q
```
You should see a "Detected UUID" with "Application: CanBoot"
![image png 4f105b5a3ffc5e8cc67c9b1901152288](https://github.com/baz-snow-ss/Voron-2.4-R2/assets/99566898/5728c898-574f-4826-b053-7114e665c584)

If you see the above, take note of the UUID and move on to flashing Klipper to the toolhead board.

### Installing Klipper
```
cd ~/klipper/
make menuconfig
```
### Klipper when using CanBoot
![image png 623b5ea3ee63b57129722a63d5995593](https://github.com/baz-snow-ss/Voron-2.4-R2/assets/99566898/4493907c-3ed0-4ae5-87ac-a1842f8e4820)
```
make clean
make
```
If you have CanBOOT installed
Run 
```
python3 ~/CanBoot/scripts/flash_can.py -i can0 -q
```
and take note of the CanBoot device that it shows:

![image png c56466210e61117764737cb42fa483ad](https://github.com/baz-snow-ss/Voron-2.4-R2/assets/99566898/50361937-831d-47a1-8ccb-45336e898650)
Then run the following command to install klipper firmware via CanBOOT. Use the UUID you just retrieved in the above query.
where the "-u" ID is what you found from the "flash_can.py -i can0 -q" query.
```
python3 ~/CanBoot/scripts/flash_can.py -i can0 -u 1e0d36d3d925-f ~/klipper/out/klipper.bin
```
![image thumb png 8ff659ce12531da2caca5b5f040a3967](https://github.com/baz-snow-ss/Voron-2.4-R2/assets/99566898/50704144-482c-4793-bfb7-c313c7bb9caa)
One the flash has been completed you can run the
```
python3 ~/CanBoot/scripts/flash_can.py -i can0 -q
```
command again. This time you should see the same UUID but with "Application: Klipper" instead of "Application: CanBoot"


### If you don't have CanBOOT installed
Then simply run the following commands to change to the klipper directory then flash the toolhead board.
```
cd ~/klipper
make flash FLASH_DEVICE=0483:df11
```
where the FLASH_DEVICE ID is the address of the USB device you noted down above.

### Klipper is now installed
You can now run the Klipper canbus query to retrieve the canbus_uuid of your toolhead board:
```
~/klippy-env/bin/python ~/klipper/scripts/canbus_query.py can0
```
![image png 33196036442d0b3582c70bd9d6d0d926](https://github.com/baz-snow-ss/Voron-2.4-R2/assets/99566898/dcdf5f53-0082-40e1-bb6f-da7ab07ba8a1)

NOTE when using CANBus you need to use a Jumper on the 120R Jumper on the SB2209 (and the Mainboard M8P in my example)
