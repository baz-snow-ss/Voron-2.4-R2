This is the procedure we follow for getting CANBUS working on the Manta MP8 v1.1s/CB1:

1. Mainboard flashing BTT Manta M8P with CB1 v1.1 
2. Flash CanBoot
3. Klipper Config uses USB-CAN-Bridge
4. Install Klipper
5. Configure interface.d 
6. Toolhead Flashing BTT SB2209 CanBus
7. Installing CanBOOT
8. CanBOOT Config
9. Installing Klipper
10. Retrive the two UUID's for your config
11. Add the configuration of this motherboard in the "printer. cfg" file:

Create and Edit the CanBus Network file.
```
sudo nano /etc/network/interfaces.d/can0
```
```
allow-hotplug can0
iface can0 can static
 bitrate 1000000
 up ifconfig $IFACE txqueuelen 1024
 ```
 Hit Q and Y
 

Edit the printer.cfg enter the correct CanBus UUID 
```
[include sb2209-canbus.cfg]
[mcu]
#serial /dev/serial/by-id/usb-Klipper_firmware_12345-if00
canbus_uuid: f7ee535c8aa0
```
Create sb2209-canbus.cfg or what ever you wish to name it.
```
## sb2209-canbus.cfg
[mcu EBBCan]
canbus_uuid: 1e0d36d3d925
```

![image png 01dca6a9cf7c28244cc9e473025b6ffc](https://github.com/baz-snow-ss/Voron-2.4-R2/assets/99566898/39b7f625-d04a-4c81-ba64-ccab84a1a2df)
### Entering DFU Mode
1. Connect the USB cable from the CB1/Pi and insert the 5V Jumper
2. Press and hold the `RESET` and `BOOT` buttons down
3. Release `RESET` button
4. Release `BOOT` button
5. The device should now be in DFU mode. Verify this via the `lsusb` command, which should look something like this:

```
Bus 001 Device 005: ID 0483:df11 STMicroelectronics STM Device in DFU Mode
```

Wire up the CAN bus using a twisted pair between the manta and your other boards.
See the Manta M8P v1.1 pinout on page 11 of the manual.
The M8P V1.1 has two identical CAN ports that are [ CAN-L, CAN-H ], [ CAN-L, CAN-H ]

Pins: L (PD12) / H (PD13)

Wire the power  Directly to the power supply 24v
Make sure to verify that you have 120ohm resistors terminating the bus, and that your other boards

are flashed for CANBUS communication at the same speed (1000000)
NOTE: when using CANBus you need to use a Jumper on the 120R Jumper on the SB2209 (and the Mainboard M8P)


Configure the module's specific functions according to your parts, and needs.
