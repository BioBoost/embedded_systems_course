Explain the difference between a Host machine and a Target machine. Also explain development machine.

Students should buy their own SD card.

rpi-hello needs a script to inject it into an IMG file. We should allow students to add description to script so they can identify their own PI.


## Assignments:

Assignment
Install the git package and create a local clone inside your homedir (currently the user pi) of the repository https://github.com/BioBoost/multimediatechnieken

Assignment
Install the apache package and find out where the webpages are stored. Make sure you can view your PI’s website from your host machine. Change the index.html page (you can use the nano editor for this) and add some cool things to it.



## Course overview

### Session 1 (about 1 hour)

Introduction into course

* Whats it about
* ECTS + credits
* Assignments after labs and multiple choice tests before labs (also create the user accounts)
* Our goals by the end of the semester

Introduction embedded systems we will use

* Raspberry Pi with Linux
* mbed without OS


### Session 2 (about 2 hours)

Virtual Machines

* Introduction into VMs
* Installing Virtual Box
* Installing Mint 18
* Installing guest additions
* Tweaking some settings
* ASSIGNMENT: change some settings, look around, find some help, ...


### Session 3 (about 4 hours)

Introduction into linux

* Basics of linux
* File system and traversing
* Editing files
* User accounts, groups and permissions
* Installing software
* ASSIGNMENT: small report on permissions and file creation
* Writing scripts
* ASSIGNMENT: write small script

### Session 4 (about 4 hours)

Raspberry Pi as embedded system and embedded operating systems

* Raspbian
* Creating SD and booting the PI
* Scanning network
* SSH connection and remote login
* Hello injector + writing new image
* Secure CoPy (SCP)


### Session 5 (about 2 hours)

Setting up the development environment

* Installing git, gcc, build-essentials, ...

Introduction into GIT

* Basics of GIT (see slides last year)
* Creating a test repository
* Pushing to github
* Cloning existing repositories

### Session 6 (about 2 hours)

Compilation and makefiles

* Creating a hello world app in c++
* Versioning using GIT + development library
* Compiling the app
* Introduction makefiles
* Creating a simple makefile for hello world

### Session 7 (about 2 hours)

The linux kernel

* Compiling the linux kernel for VM
* Installing cross-compilation tools and build-tools
* Cross-compiling linux kernel for ARM
* Deploying a new kernel


### Session 8 (about 4 hours)

Kernel development and hello module

* Introduction into loadable kernel modules
* Creating a simple LKM (hello world)
* Cross-compiling the module
* Loading the module


### Session 9 (about 4 hours)

Loadable kernel modules

* Internal memory loadable kernel module
* I2C Memory module
* Neopixel module with external mbed

Is this feasible ? Maybe this might be too much.
