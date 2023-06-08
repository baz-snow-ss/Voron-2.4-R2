### Toolhead Flashing BTT SB2209 CanBus
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

