---
description: This chapter contains a walk-through on how to setup a Linux virtual machine. This VM is used throughout the course as a development platform.
---

# Running a Virtual Development Machine

A virtual machine (VM) is an emulation of a particular computer system. This system can be based on an existing or hypothetical machine. As a user we can create such virtual machines and install an operating system of choice on them. This allows us to run a Linux distribution while working on a Windows machine and vice versa.

Several software packages are available to create and run virtual machines. Examples are VMware, Hyper-V which comes with Windows 8, Oracle VirtualBox, ... For our labs we will be using VirtualBox as this is free, lightweight, easy to use and available for Windows, Linux, Mac and Solaris.

{% hint style="note" %}
**Hyper-V**

Hyper-V, codenamed Viridian and formerly known as Windows Server Virtualization, is a native hypervisor; it can create virtual machines on x86-64 systems. Starting with Windows 8, Hyper-V supersedes Windows Virtual PC as the hardware virtualization component of the client editions of Windows NT.
{% endhint %}

## Installing Virtual Box

Start by going to the download section of the website of VirtualBox ([https://www.virtualbox.org](https://www.virtualbox.org)). Download the VirtualBox platform package for your system. At the moment of this writing the current version of VirtualBox is 5.1.28. When running the installer package make sure to install VirtualBox with all features enabled as shown in the figure below.

![Installing VirtualBox with all features enabled](img/virtual_box_install.png)

It is also necessary to install the extension pack which introduces USB2.0 and USB3.0 support and some other extra features. You can download the extension pack on the same page as you downloaded the installer for VirtualBox. Just make sure to pick the correct version for your current VirtualBox version. To install the extension pack just open the file by double clicking it.

The installer of VirtualBox should also have created virtual network adapters (such as can be seen in the figure below) which are used for private networking between the host machine and the virtual machine.

![VirtualBox virtual network adapters](img/virtual_box_network_adapters.png)

{% hint style="alert" %}
**Virtual Box Network Adapters**

In some cases these network adapters may interfere with the correct working of your physical network. For example for LAN-games that use UDP broadcasting to discover each other. In this case you can just disable the adapters. Make sure to re-enable them before using VirtualBox.
{% endhint %}

Once finished you can start the VirtualBox client and you should get the interface shown in the figure below:

![Launching VirtualBox after installation](img/virtual_box_clean_install.png)

Under `Files => Preferences => General` you can change the default path for your virtual machines. Do take note that you will need about 25GB of free space for each VM. For these labs you will most likely only need 1 VM.

Under `Files => Preferences => Language` you can also change the default interface language if you wish.

## Creating a Virtual Machine

Creating a virtual machine is very simple as it is just following the steps presented to you by the wizard. To start the process of creating a VM hit the *New* button on the main interface of VirtualBox.

The first step consist of giving your VM a name and selecting the operating system you will be running on the VM as shown in the figure below. In our case we will use Linux Mint 18 (Sarah) - Cinnamon (64-bit). Select **Linux** as type and **Ubuntu (64-bit)** as version.

![Creating a VM - The name and OS](img/vm_new.png)

Next we need to select the amount of memory we want to assign to the virtual machine. The recommended amount is 1024MB. However if you have more than 4GB, select 2048MB, which will improve the responsiveness and performance of the VM.

![Creating a VM - Amount of memory](img/vm_memory.png)

Next we need to choose if we want to create a new or use an existing virtual hard drive. Pick the option to create one now as depicted in the figure below. This will launch another wizard which will lead us through the creation process of a virtual drive.

![Creating a VM - Virtual hard drive](img/vm_hdd.png)

The first screen will allow us to select what type of virtual drive file we want to create. Just leave the default option (VDI – Virtual Disk Image).

![Creating a VM - Hard drive file type](img/vm_vdi.png)

In the next step we get the option to create a dynamically allocated image or a fixed size image as can be seen in the figure below. A fixed size image is faster but will take up the full space we select for the size of the virtual drive. A dynamically allocated image is "slower" but will only grow in size when needed. You will need to decide this for yourself based on the free space available on your host system. However the default option is probable the best.

![Creating a VM - Virtual drive allocation method](img/vm_dynamic.png)

Now we need to select the hard drive file location (leave it as is) and size of the drive. Make sure to select **at least 20GB for the size** as shown in the figure below. Hitting create will finish the process of creating a VM.

{% hint style="warning" %}
**Image resize**

If you chose to create a dynamically allocated image you can make it even bigger as it will only use as much space as needed. Resizing an existing image can be a real pain and can also corrupt your image so make sure you don't have to do this.
{% endhint %}

![Creating a VM - Location and size of the virtual drive](img/vm_size.png)

Your new VM should now appear in the list of VM's on the left side of the VirtualBox main interface. Selecting a VM in the list also displays some basic information about the VM.

![A new VM is added to your current list of VM's](img/vm_created.png)

## Configuring the Virtual Machine

Before installing an operating system on the newly created VM it is necessary to make a few configuration changes. Select the new VM and hit the *Settings* button on the main interface. You will be presented with the configuration settings of your VM.

Start by going to `General => Advanced` and enabling the *bidirectional shared clipboard*. This allows text to be copied to the clipboard in the VM and pasted in your host OS and vice versa. Also enable *bidirectional Drag'n Drop*. The resulting configuration is shown in the figure below.

![Configure VM to allow bidirectional clipboard and drag'n drop](img/vm_config_clipboard.png)

If you have multiple virtual machines it is also a good idea to add a description the VM. This may come in handy later when you do a cleanup of virtual machines. This can be done via `General => Description`

![Adding a Description to the VM](img/vm_description.png)

You should also enable *3D acceleration* which can be found under `Display => Screen` as shown in the image below. If you have a decent graphics card in your host, you can also slightly increase the *Video Memory* from 16MB to for example 24MB or 32MB.

![Configure VM to enable 3D hardware acceleration](img/vm_enable_3d_acceleration.png)

Default the VM is configured with a single network adapter with NAT (Network Address Translation) enabled (Network tab). This means that the VM has access to the network and also has access to the Internet. However because of NAT we will not be able to connect to the VM from another machine using SSH without configuring port forwarding. Since later on in the LAB's we will need to do just that, it is more convenient to change the network settings to a *Bridged Adapter* as shown in the figure below. Make sure to select your physical Ethernet adapter to bridge with and not your wireless interface.

![Configure VM to bridge the virtual and physical adapters](img/vm_config_bridged.png)

{% hint style="note" %}
**Bridged Adapter**

This will basically create a network bridge between the VM's network adapter and your physical host adapter making your VM's directly available on your network. This may be a security issue but can also simplify working with your VM. This option also implies that your VM will get its IP address from the same DHCP (Dynamic Host Configuration Protocol) server as your host machine if you have a DHCP enabled network.
{% endhint %}

Some files can be dragged and dropped between your host machine and the VM. However this does not seem to be possible for all file types. For these instances it is more convenient to create a shared directory which can be accessed from your host and the VM.

Navigate to `Settings => Shared Folders` of your virtual machine and create a shared folder on your system (for example your desktop) and make sure to select the **Auto-mount** option and not Read-only as shown in the figure below.

![Creating a shared folder for your VM](img/mint_shared_folder.png)

The folder should now be automatically mounted under /media in your VM on your next reboot and should also be available on the Desktop of Linux Mint.

## Installing an Operating System on the Virtual Machine

Before we can install an operating system on our virtual machine, it is necessary to download an installation image for the Linux distribution we will be using. This image can then be mounted on our VM allowing us to boot from it. In our case we will use Linux Mint 18.2 (Sonya) - Cinnamon (64-bit), which can be downloaded from [https://www.linuxmint.com/download.php](https://www.linuxmint.com/download.php). Make sure to select the 64-bit Desktop edition. Linux Mint is derivative of Ubuntu, but with a less intrusive graphical desktop environment.

Once downloaded start VirtualBox and open the settings of your VM. Next open the storage settings. Now select the virtual CD/DVD drive below the IDE controller as shown in step 1 in the figure below:

![Steps for mounting an image in VirtualBox](img/vm_mount_iso.png)

Hit the small CD/DVD icon next to the IDE Secondary Master dropdown (step 2 in the figure above) and select **Choose a virtual CD/DVD disk file ...** A browse window will open; select the image file you downloaded from the Linux Mint website and hit OK. Hit the OK button of the setting panel to close it.

Ready ? Then hit the start button of the VM and follow the steps for installing the Linux Mint operating system. If you see the automatic boot screen shown below do nothing and let it pass. Linux Mint will boot in Live mode and allow you to start the install process from that point on.

![Live DVD or boot menu option](img/mint_first_boot.png)

You can ignore the message about running in software rendering mode for the moment. We will fix this later.

To start the installation process just double click the 'Install Linux Mint' icon on the desktop. From this point on all steps are self-explanatory. Most of the installation steps can be kept to their default values.

You will get the question if you would like to install third-party drivers. While this is a VM and I am not 100% sure about this, I selected the option to do it.

![Third-pary drivers](img/mint_third_party_hardware.png)

As a last step you will need to create a user account. Make sure to enter a **decent password** (no spaces or blanks and such crap).

If you click inside the VM window your mouse cursor will automatically be captured. Releasing your cursor can be achieved using the right CTRL key.

Once the installation procedure is finished you will be asked to reboot the VM.

## Installing Guest Additions

You may or may not have noticed that your mouse movement is a bit sluggish within the VM. That is because the guest additions haven not been installed yet.

Before installing the new guest additions we need to remove the old ones that come with the Linux distro. This can be accompished by opening up a terminal (via menu or `CTRL-ALT-T`) and issueing the following command:

```shell
sudo apt-get remove virtualbox-guest-utils
```

You will be requested to enter your password. Restart the VM after doing this.

Once restarted, open the **Devices** menu which can be found at the top of the VM window. Next select **Insert Guest Additions CD image ...** as shown in the figure below. A window in Linux will open asking if you'd wish to run the package. Hit run and follow the instructions.

![Inserting the Guest Additions for Linux Mint](img/mint_guest_additions.png)

Once finished remove the image from the virtual drive (by right clicking the icon on the Desktop of Linux Mint and choosing Eject). Restart the virtual machine.

{% hint style="warning" %}
**Updates**

Every time you update your machine it is necessary to repeat this procedure. While it is strongly encouraged to keep your VM up-to-date as any machine, it might be a good idea to do this at a decent moment, for example at home after the lessons and not at the start of a LAB.
{% endhint %}

You should now be able to resize the guest window. Or you can switch to full screen by hitting `RCTRL-F`. Same key-combination to switch back to windowed mode.

{% hint style="note" %}
**Software Rendering Mode**

If you get a popup after login saying that Linux Mint is running in software rendering mode, you may have forgotten to enable 3D acceleration. This can be fixed by going to `Settings => Display => Screen` and enabling **3D Acceleration** of your virtual machine. You may also need to increase the video memory a bit to for example **32MB**. Make sure to restart the VM after changing these settings.
{% endhint %}

## Creating a shared folder

While we did instruct VirtualBox to mount a shared folder from Windows to Linux, it is not yet accessible. If you try to open the folder you will get a permission error. To fix this it is necessary to add your current user to the group `vboxsf`. You can achieve this by opening up a terminal (`CTRL-ALT-T`) and entering the command below. More on this later.

```shell
sudo usermod -a -G vboxsf <your_account_name>
```

Next logout from the current session and log back in. Open up a new terminal and enter the `id` command to get a list of all the groups your user belongs to. `129(vboxsf)` should be one of them.

```shell
id
uid=1000(bioboost) gid=1000(bioboost) groups=1000(bioboost),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),113(lpadmin),129(vboxsf),130(sambashare)
```

## Backups and snapshots

It is always a good idea to backup your project files. You can now easily copy them to the shared folder and put them on OneDrive, DropBox or a USB stick.

Another good idea is to create periodical snapshots of your virtual machine. This snapshot will contain all the delta's compared to the previous state of the VM. Just select the VM you want to create a snapshot of and hit the **Snapshots** button in the top right corner of VirtualBox. Next click the left icon to create a snapshot. Fill in the name and description as shown in the figure below.

![Creating a VM snapshot](img/vm_snapshot.png)

Adding a decent description will save you the misery of having to search through the snapshots for the correct state if your VM should fail.

You could even do this after every LAB session.
