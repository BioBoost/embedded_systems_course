# TouchBerry Pi Controller

The TouchBerry Pi shield is a shield that can be plugged on top of the Raspberry Pi (2 and 3) and enables a touch interface through 7 capacitive buttons. The shield has the following hardware on board:

* **version 1.0:**
  * AT42QT1070 I2C capacitive touch sensor IC
  * TLC59116 I2C-Bus Constant-Current LED Sink Driver
  * 5 RGB LEDs

* **version 2.0:**
  * AT42QT1070 I2C capacitive touch sensor IC
  * TLC59116 I2C-Bus Constant-Current LED Sink Driver
  * 5 RGB LEDs
  * 24LC65 64K I2C Smart Serial EEPROM
  * MCP9800 2-Wire High-Accuracy Temperature Sensor
  * MMA8451QT MEMS Accelerometer, 3-Axis

You can find the schematic and component list on the project page at CircuitMaker [https://circuitmaker.com/Projects/Details/Sille-Van-Landschoot-2/TouchBerry-Pi](https://circuitmaker.com/Projects/Details/Sille-Van-Landschoot-2/TouchBerry-Pi).

## TouchBerry Shield Driver

Create a C++ application for the Raspberry Pi that can read the status of the touch sensor (AT42QT1070 which is at address `0x1B`). Make sure you can perform the following actions on the IC:

* Read the vendor ID
* Read the value of the pressed key register (only single pad can be touched at the same time)
* Calibrate the device
* Reset the device

Next extend the application so you can also control the 5 RGB LEDs of the shield (via the TLC59116 I2C LED driver - which is at address `0x60`). This device is a bit complicated so make sure to read the datasheet. Considerations to make are:

* Make sure to turn the oscillator on
* Make sure that the PWM is enabled
* Write a value to the brightness control registers

Do al of this in a object oriented manner so your code can be easily extended.

<!-- Solution for the teacher to this can be found @ [https://github.com/BioBoost/touch_berry_shield_cpp](https://github.com/BioBoost/touch_berry_shield_cpp) -->

## Publishing via MQTT

The pressed keys from the QT1070 shield be published via MQTT. This way they can be used to control anything (the Thumper, NUBG, ...).

For this you can make use of the following library: [https://github.com/BioBoost/simple_mqtt_client](https://github.com/BioBoost/simple_mqtt_client).

The required libraries need to be installed on the Raspberry Pi because they need to be dynamically loaded. While all these steps are documented with the libraries, here is an overview:

```shell
# Tools
apt-get update
apt-get -qy install git build-essential gcc make cmake cmake-gui cmake-curses-gui libssl-dev libcurl4-gnutls-dev dh-autoreconf wget

# Paho MQTT C
cd /usr/local/src
git clone https://github.com/eclipse/paho.mqtt.c.git
cd paho.mqtt.c
git checkout v1.2.1
make
make install

# Paho MQTT C++
cd /usr/local/src
git clone https://github.com/eclipse/paho.mqtt.cpp.git
cd paho.mqtt.cpp
git checkout v1.0.0
make

# Install paho C++ libraries (no make install)
cd /usr/local/src/paho.mqtt.cpp
install -m 644 lib/libpaho-mqttpp3.so.1.0.0 /usr/local/lib/
/sbin/ldconfig /usr/local/lib
cd /usr/local/lib
ln -s libpaho-mqttpp3.so.1 libpaho-mqttpp3.so
cp -r /usr/local/src/paho.mqtt.cpp/src/mqtt /usr/local/include
chmod 644 /usr/local/include/mqtt/*
echo '/usr/local/lib' | tee /etc/ld.so.conf.d/mqttpp.conf
/sbin/ldconfig

# JSON
cd /usr/local/include/
wget https://raw.githubusercontent.com/nlohmann/json/v3.2.0/single_include/nlohmann/json.hpp

# Cpp RestClient - Doesnt seem like hes using tags like he should
cd /usr/local/src
git clone https://github.com/mrtazz/restclient-cpp.git
cd restclient-cpp
git checkout bdf5335cae8b879a8116cb8053828aff5d9544d2
./autogen.sh
./configure
make install

# Bios Logger
cd /usr/local/src
git clone https://github.com/BioBoost/bios_logger.git
cd bios_logger
git checkout v1.1.1
make
make install

# Simple MQTT Client
cd /usr/local/src
git clone https://github.com/BioBoost/simple_mqtt_client.git
cd simple_mqtt_client
git checkout v1.0.0
make
make install
```

When compiling on the Raspberry Pi no special actions are needed as the native compiler is just `g++` (not the cross-compiler) and it will automatically look for the library in `/usr/local/lib`.

## Native compiling on the Raspberry Pi

Using the libraries requires you to add the libraries to the compilation steps. For this you can use the more advanced `Makefile` shown below as an example. Notice the `LIBS=-lpaho-mqttpp3 ...` variable. If you use the makefile shown below as is, you will need to place all you code files inside a subdirectory `src`. You will find the resulting binary in the `bin` directory.

You will need to save the following code inside a file called `Makefile` directly below the root of your project folder.

```Makefile
# The compiler to use
CC=$(CCPREFIX)g++

# Compiler flags
CFLAGS=-c -Wall -std=c++11
    # -c: Compile or assemble the source files, but do not link. The linking stage simply is not done. The ultimate output is in the form of an object file for each source file.
    # -Wall: This enables all the warnings about constructions that some users consider questionable, and that are easy to avoid (or modify to prevent the warning), even in conjunction with macros.

# LDFLAGS=

# Libraries
LIBS=-lpaho-mqttpp3 -lpaho-mqtt3a -lbioslogger -lsimple_mqtt_client

# Name of executable output
TARGET=nubg_touchberry
SRCDIR=src
BUILDDIR=bin

OBJS := $(patsubst %.cpp,%.o,$(shell find $(SRCDIR) -name '*.cpp'))

all: makebuildir $(TARGET)

$(TARGET) : $(OBJS)
	$(CC) $(LDFLAGS) -o $(BUILDDIR)/$@ $(OBJS) $(LIBS)

%.o : %.cpp
	$(CC) $(CFLAGS) $< -o $@

clean :
	rm -rf $(BUILDDIR)
	rm -f $(OBJS)

makebuildir:
	mkdir -p $(BUILDDIR)
```

To execute the make file all you need to do is execute the `make` command inside your project folder.

## JSON

Want to use a handy library for the JSON that needs to be used for publishing your key presses then checkout [https://github.com/nlohmann/json](https://github.com/nlohmann/json).

It's a header only version so not a lot of work to install it.

## Documentation

Make sure to document your project with a `README.md`.