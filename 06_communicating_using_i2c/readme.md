---
description: Connecting a device to another device using I2C is a common practice. This chapter introduces the basics.
---

# Communicating using I2C

Why I2C? Because

* Its a common standard
* Its "fast" for low-speed devices
* Bus (multiple devices can be connected)
* Easy to use
* Wide support
* Only 2 communication lines needed (SDA and SCL)
 * SCL is the clock line.
* It is used to synchronize all data transfers over the I2C bus.
 * SDA is the data line.
* The SCL and SDA lines are connected to all devices on the I2C bus
* There does need to be a third wire which is the ground

![I2C Bus](img/bus.gif)

Both SCL and SDA lines are "open drain" drivers. What this means is that the chip can drive its output low, but it cannot drive it high. For the line to be able to go high you must provide pull-up resistors to Vcc. There should be a resistor from the SCL line to Vcc and another from the SDA line to Vcc. You only need one set of pull-up resistors for the whole I2C bus, not for each device. Vcc depends on the devices used. Typically 5V or 3V3

The devices on the I2C bus are either masters or slaves. The master is always the device that drives the SCL clock line. The slaves are the devices that respond to the master. A slave cannot initiate a transfer over the I2C bus, only a master can do that. There can be, and usually are, multiple slaves on the I2C bus, however there is normally only one master. It is possible to have multiple masters, but it is unusual. Slaves will never initiate a transfer. Both master and slave can transfer data over the I2C bus, but that transfer is always controlled by the master.

Want to know more about I2C, then checkout [https://learn.sparkfun.com/tutorials/i2c](https://learn.sparkfun.com/tutorials/i2c).

## Connecting a Raspberry Pi 3 to an mBed via I2C

Voltage levels should always be checked.

* Raspberry Pi runs at 3.3V
* mBed runs at 3.3V
* So no level shifting is required

Normally I2C requires you to add a pull-up resistor to each line (SDA and SCL). However in the case of connecting an mbed to a Raspberry Pi 3 you will not need to add these. This because the Raspberry Pi 3 already has pull-ups of 1k8 on each i2c line.

![Bidirectional Level Shifter](img/bidirectional_level_shifter.png)

For example to connect the Raspberry Pi I2C to the mBed I2C we can use the following connection diagram.

![Connecting RPi3 to mBed via I2C](img/i2c_connection_pi_mbed.png)

The pinout of all Raspberry Pi models can be found at [https://pinout.xyz/](https://pinout.xyz/).

> **HINT** - **mBed I2C channels**
>
> Note that the mBed has multiple I2C channels. Make sure to use the one that is selected in the firmware.

## I2c-tools

i2c-tools is a useful package that allows us to scan the I2C bus for devices. Very useful for debugging and testing.

> **HINT** - **Enabling i2c on Raspberry Pi**
>
> Make sure to enable the i2c bus on the Raspberry Pi. This can be achieved using the `raspi-config` tool by selecting `Interface Options => I2C => Enable`. You may need to restart the device.

Install the tools using the following command:

```shell
sudo apt update
sudo apt install i2c-tools
```

From now on the `i2cdetect` tool can be used to scan the i2c bus and detect connected slave devices. First check the directory `/dev` for available i2c busses

```shell
cd /dev
ls i2c-*
```

Look for `i2c-x` where x is a number

By using`i2cdetect -r x` and replacing `x` with the number of the actual device bus, the bus can be scanned. For example:

```shell
i2cdetect -r 1
```

You should get output similar to the one shown below if nothing is externally connected.

```text
WARNING! This program can confuse your I2C bus, cause data loss and worse!
I will probe file /dev/i2c-1 using read byte commands.
I will probe address range 0x03-0x77.
Continue? [Y/n]
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:          -- -- -- -- -- -- -- -- -- -- -- -- --
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
70: -- -- -- -- -- -- -- --
```

Do note that Linux uses 7-bit addresses for I2C (the R/W LSB-bit is dropped off). That is why it seems that only half of the range is scanned.

When you connect the mBed LPC1768 device (mounted on top of an application board) to the Raspberry Pi via I2C you should get the following output when scanning the bus.

```text
WARNING! This program can confuse your I2C bus, cause data loss and worse!
I will probe file /dev/i2c-1 using read byte commands.
I will probe address range 0x03-0x77.
Continue? [Y/n]
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:          -- -- -- -- -- -- -- -- -- -- -- -- --
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
40: -- -- -- -- -- -- -- -- 48 -- -- -- 4c -- -- --
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
70: -- -- -- -- -- -- -- --
```

The device at address `0x48` is the temperature sensor and the accelerometer has an address of `0x4c`. Both are chips available on the application board. Now turn around the application board and check the addresses that are specified there.

## I2C from C++

Communicating with I2C devices can be achieved from many programming languages. C++ is one of them.

> **HINT** - **Use C++ 11 standard**
> 
> Make sure to compile your application using the C++ 11 standard. Pass this to the compiler when compiling your application. For example: `g++ -std=c++11 main.cpp -o i2c`

### Opening the Bus

In order to communicate with an I2C peripheral one must first open the bus for reading and writing like you would any file. A call to `open` must be used rather than `fopen` so that writes to the bus are not buffered. This functions returns a new file descriptor (a non-negative integer) which can then be used to configure the bus.

A typical reason for failure at this stage is a lack of permissions to access the device file. Adding the user to a group which has permissions to access the file will alleviate this problem, as will adjusting the file permissions to enable user access.

```c++
const std::string DEVICE = "/dev/i2c-1";

int i2cfile;
if ((i2cfile = open(DEVICE.c_str(), O_RDWR)) < 0) {
    cout << "Could not open bus" << endl;
    exit(1);
}
cout << "Successfully opened the i2c bus" << endl;

// ...

// Make sure to close the handle
close(i2cfile);
```

### Initiating communication

After successfully acquiring bus access, you must initiate communication with whatever peripheral you are attempting to utilize. I2C does this by sending out the seven bit address of the device followed by a read/write bit. The bit is set to 0 for writes and 1 for reads. This is another common failure point, as manufacturers tend to report the I2C address of the peripheral in a variety of ways. Some report the address as a seven bit number, meaning that the address must be shifted left by a bit and then have the R/W bit tacked onto the end. Others will provide it as an eight bit number and assume you will set the last bit accordingly. Although a few manufacturers actually say which method they use to describe the address, the vast majority do not, and the user may have to resort to testing via trial and error.

> **HINT** - **i2cdetect**
> 
> Again this is where i2cdetect comes in handy. Just hang an RPi to the bus and scan it.

The calls to `read()` and `write()` after the `ioctl()` will automatically set the proper read and write bit when signaling the peripheral.

```c++
const int SLAVE_ADDRESS = 0x32;          // The I2C address of the slave device
if (ioctl(i2cfile, I2C_SLAVE, SLAVE_ADDRESS) < 0) {
    cout << "Failed to acquire bus access and/or talk to slave." << endl;
    exit(1);
}
cout << "Ready to communicate with slave device" << endl;
```

### Reading from the slave device

The `read()` function wraps a read system call is used to obtain data from the I2C peripheral. Read requires a file handle, a buffer to store the data, and a number of bytes to read. The `read()` function will attempt to read the number of bytes specified and will return the actual number of bytes read, which can be used to detect errors.

```c++
const unsigned int BUFFER_SIZE = 10;
// ...
char buffer[BUFFER_SIZE] = { 0 };
if (read(i2cfile, buffer, 2) != 2) {
    cout << "Failed to read from the i2c device." << endl;
} else {
    cout << "Read from device: ";
    cout << std::hex << "0x" << (int)buffer[0] << " 0x" << (int)buffer[1] << endl;
}
```

### Writing to the slave device

The `write()` function that wraps a write system call is used to send data to the I2C peripheral. The `write()` function requires a file handle, a buffer in which the data is stored, and a number of bytes to write. It will attempt to write the number of bytes specified and will return the actual number of bytes written, which can be used to detect errors.

Some devices require an internal address to be sent prior to the data to specify the register on the external device to access. For devices with more than one configuration register, the address of the register should be written first, followed by the data to be placed there. See the datasheet of your peripheral for specifics.

```c++
char buffer[] = { 0x00 };
if (write(i2cfile, buffer, 1) != 1) {
    cout << "Failed to write to the i2c device." << endl;
} else {
    cout << "Successfully wrote to the i2c device." << endl;
}
```

### Waiting between transactions

When executing multiple read and/or write transactions, it may be necessary to wait between these transactions. This depends on the speed of the peripheral.

The problem in C++ is that there was no real standard before C++ 11. Windows implementations differ strongly from Unix ones, and so do their precision's. On top of this, neither operating system is a real-time operating system, so requesting to sleep for 10 milliseconds may result in a real sleep of 500 milliseconds.

As C++ 11 is used here, you can make use of the code snippet below:

```C++
#include <chrono>
#include <thread>

//...

std::this_thread::sleep_for(std::chrono::milliseconds(100));
```

This function may block for longer than `sleep_duration` due to scheduling or resource contention delays. The standard recommends that a steady clock is used to measure the duration. If an implementation uses a system clock instead, the wait time may also be sensitive to clock adjustments.

## Reading application board temperature sensor

Attaching the mBed with its application board, also provides access to an LM75B temperature sensor. This device is present at I2C address `0x48` (7-bit). The code below can be used to read its value in degrees.

```c++
// Compile using g++ -std=c++11 main.cpp -o i2c
#include <iostream>
#include <unistd.h>   // close
#include <fcntl.h>    // O_RDWR
#include <sys/ioctl.h>
#include <linux/i2c-dev.h>  // I2C_SLAVE

using namespace std;

int main(void) {
  const std::string DEVICE = "/dev/i2c-1";
  const unsigned int BUFFER_SIZE = 10;

  int i2cfile;
  if ((i2cfile = open(DEVICE.c_str(), O_RDWR)) < 0) {
      cout << "Could not open bus" << endl;
      exit(1);
  }
  cout << "Successfully opened the i2c bus" << endl;

  const int SLAVE_ADDRESS = 0x48;          // The I2C address of the slave device
  if (ioctl(i2cfile, I2C_SLAVE, SLAVE_ADDRESS) < 0) {
      cout << "Failed to acquire bus access and/or talk to slave." << endl;
      exit(1);
  }
  cout << "Ready to communicate with slave device" << endl;

  char buffer[BUFFER_SIZE] = { 0 };
  if (read(i2cfile, buffer, 2) != 2) {
      cout << "Failed to read from the i2c device." << endl;
  } else {
      cout << "Read from device: ";
      double temperature = ((buffer[0] << 8) | (buffer[1])) / 256.0;
      cout << "The current temperature is " << temperature << " degrees Celsius" << endl;
  }

  // Make sure to close the handle
  close(i2cfile);

  return 0;
}
```