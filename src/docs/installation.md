

## Installation

**Prerequisites**

Minimum requirements to start the process are as follows: Intel NUC, Bootable USB Stick, USB Keyboard and Mouse, Monitor, HDMI Cable  and Internet Network Connection.

What we're about to do is, download the Ubuntu operating system 'image', put it on a USB stick, then put the stick into the server (NUC) to install the operating system.

Rufus is the software we'll use to create Bootable Media (the USB stick with Ubuntu). 



## Install Ubuntu Server on NUC

### 1. Download Ubuntu Server
* Download the latest Ubuntu Server 20.04 LTS .iso image using the Ubuntu-Server [link](https://ubuntu.com/download/server). 
* Click Option 2 - Manual server installation.
![](https://i.imgur.com/KVn3rV1.png)

* Click "Download Ubuntu Server 20.04.2 LTS"
![](https://i.imgur.com/Kv0stXZ.png)



### 2. Create Bootable USB Drive


* Download Rufus to create a bootable USB drive. Click [here](http://rufus.ie/en/), then download the exe file:

![](https://i.imgur.com/BK7acFt.png)

* Run the rufux.exe file. Select the device (injected USB) and click on ‘select’ to select your downloaded ubuntu iso file.

* Leave the rest of the options to default settings.
**  Partition Scheme: MBR
** Target System: NIOS or UEFI

* Click Start and press OK for further instructions.

* If you get stuck, you can follow [these](https://ubuntu.com/tutorials/create-a-usb-stick-on-windows#1-overview) instructions.
* Note: There are other options/ways as well to create a bootable USB drive

### 2.5 (Alternative for Mac/Linux)

* Install https://www.balena.io/etcher/
* Tips: https://www.youtube.com/watch?v=f78AwZk3IXs
![image](https://user-images.githubusercontent.com/36915805/129319222-685581ea-9a27-4ee2-acb4-7d24076499d3.png)


### 3. Install Ubuntu Server on the NUC

* Insert the network cable from internet into the NUC.
* Insert the bootable USB stick you just made.
* Turn on the NUC (square button in front). The light should turn blue.
* **Optional:** This step may not be required for a few systems (Asus systems may require adjustments). Press F2 to enter into the UEFI BIOS set-up menu and check settings. Press F10 to save changed settings. NUC will restart on its own.

**Note:** F2 > Boot-Order (UEFI) > Advanced > Boot-Configuration > OS=Linux and Boot Device=USB and Secure Boot should be enabled.

**To enter boot menu:**

![](https://i.imgur.com/FgdVCKs.png)


* Press F10 to enter into the boot menu
* Select the USB memory stick (UEFI)
* Select ‘Install Ubuntu Server’. You can navigate through the options with the arrow keys. Press Enter to select the desired option. 
![](https://i.imgur.com/kOa4AwA.png)

* Select Language

![](https://i.imgur.com/gB2BIAL.png)


* Select Keyboard Layout

![](https://i.imgur.com/zPvstcc.png)


* Select the network cable (eth0/ens/enp)


* Click Done and continue for further instructions

* Select the right disk (should be LITEON with more space)

> Use an entire disk: Selecting this option will erase the entire hard disk and automatically partition the drive for you.

* Click Done and continue for further instructions


* Select server name, username and password.  This can be whatever you want, but make sure to write it down.

![](https://i.imgur.com/Cme8vsL.png)



* Install openSSH server and press spacebar to select 


* Nothing else needs to be installed, click on done


* It will take another 3 minutes or less for the installation


* Select reboot and hit enter. Remove the USB stick. 


* Use the username and password to login to the NUC server.


* Try to ping an internal machine participating in LAN



### Additional Installation Resources

https://www.makeuseof.com/ubuntu-server-guide/

https://www.itsupportwale.com/blog/install-ubuntu-20-04-lts-server/



### 4. Install Lokal platform on the NUC

Coming soon.
