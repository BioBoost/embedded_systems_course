## Challenges

### Periodic temperature reading

Attach the mBed with application board to the Raspberry Pi. Extend the example code given in the chapter to read the temperature of the LM75B temperature sensor every 2 seconds. Keep doing this until the user kills the application.

### mBed I2C Memory Slave

Get the firmware for the slave device from this GitHub repository [https://github.com/iot-devices-2019/mbed_memory_i2c_slave](https://github.com/iot-devices-2019/mbed_memory_i2c_slavee). The readme also contains the necessary documentation to communicate with the device.

Clone it using git and deploy it using the offline mbed-CLI. Connect to the device (115200 baud) using putty or another terminal client to see some debugging information.

> **WARNING** - 7-bit versus 8-bit addressing
>
> Caution because mBed uses 8-bit I2C addresses while Linux uses 7-bit. You will need to use the 7-bit address in you C++ program !

> **WARNING** - **Firmware update**
>
> Check the alive LED on the device. If it does not blink every 500ms but more every 5 seconds, then the device requires a firmware update. Navigate to [https://os.mbed.com/handbook/Firmware-LPC1768-LPC11U24](https://os.mbed.com/handbook/Firmware-LPC1768-LPC11U24) and download the latest firmware. Save the `mbedmicrontroller_141212.if` file to the mBed flash drive and remove the USB cable. Plug it back in and wait until the devices comes back up.

Write a user space application that stores some data inside the memory and reads it back. You can use for example an icon, a sentence or something else.

In this setup the Raspberry Pi will act as a master while the mBed memory emulator is the slave.
