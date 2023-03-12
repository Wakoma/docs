

## Installation

**Prerequisites**

Minimum requirements to start the process are as follows: Intel NUC, Bootable USB Stick, USB Keyboard and Mouse, Monitor, HDMI Cable  and Internet Network Connection.

What we're about to do is, download the Ubuntu operating system 'image', put it on a USB stick, then put the stick into the server (NUC) to install the operating system.

Rufus is the software we'll use to create Bootable Media (the USB stick with Ubuntu). 



## Install Ubuntu Server on NUC

### 1. Download Ubuntu Server
* Download the latest ISO image Ubuntu LTS Server from [official ubuntu site](https://ubuntu.com/download/server).
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



## Install Lokal platform on the NUC

Lokal is installed using `ansible` that allows you to manage your (ubuntu) server remotely. Firstly, you
need to install ansible and clone the Lokal repo on your client (can be your server - you'll perform
local installation in this case).

```bash
git checkout https://github.com/Wakoma/Lokal.git Lokal
cd Lokal
```

We require ansible version `>=2.11`. Ansible is a python package so alternatively, you can use `pip` to
install it.

```bash
pip install ansible  # or apt install ansible
ansible-galaxy install -r requirements.yml
```

In case your client is Windows or just don't want to install ansible and python on your computer,
feel free to use our docker container, that contains all necessary software for the client.
```bash
docker build -t lokal:latest .
docker run --rm -itv ${PWD}:/lokal lokal:latest
```
This will give you a bash terminal where you can access lokal files and run all commands described below.

_This is just a "client" that you use to actually install Lokal on your server. It is not Lokal installation._

### Prepare your server (optional)

This is an optional step that helps you prepare a fresh machine with only root user on it.
The play `prepare.yml` creates an application user and adds your SSH key in `authorized_keys`
if you define `setup_ssh:true` in your hosts vars. It also installs latest docker and other
necessary software. You need to create a separate inventory file because this play connects
as `root`. The content of the file is following
```YAML
all:
  hosts: "1.2.3.4"
  vars:
    app_user: ubuntu # the application user under which lokal will run
    ansible_user: root # mandatory to connect as root user
    setup_ssh: true # set to true only if app_user does not have SSH setup yet (and fill `ssh_key`)
    ssh_key: <content of your .ssh/id_rsa.pub or wherever you have your public key>
```

Now you are ready to run `ansible-playbook -i hosts/<your-inventory-file> prepare.yml`

### Install Lokal

First, create a new inventory file in `hosts/`. You can copy/modify the example `hosts/remote` file.
I use server's nickname or domain for the filename.  This file will contain the IP, domain and other
details about your server so Lokal can be setup  correctly. It needs to be in YAML format so you can
specify services in this nice way. 
The minimal example is bellow. For complete setup see [configuration section](configuration.md)

```YAML
all:
  hosts: "1.2.3.4" # your server's IP (can be a hostname too)
  vars:
    ssl_use_acme: true # or false if it is just testing machine not visible from the internet
    domain: server.example.com # domain visible from the outside internet (used only when ssl_use_acme=true)
    ansible_user: ubuntu # the application user under which Lokal will run
    services: # define wanted services - list of available services is in roles/ folder
    - wordpress
```

Other options, that can be overriden can be found in `roles/<service>/defaults/main.yml`.
The most important variables are described in [configuration section](configuration.md)
and can be found in `roles/common/defaults/main.yml`

## Services

The services are hidden in `roles` folder (ansible required naming). You can find there the `base`
service and `common` service used only as a source of configuration and optional services in other folders.
You define which services to install in your `hosts/` file (per-server). Add/remove any desired services
in `vars:services` list. No need to specify either base or common. They are both installed automatically.

### Installation

Once you have ansible setup on your local machine and remote server ready (preferably using `prepare.yml`)
then you can start using the main `playbook.yml` that by default installs all services defined in
`hosts` file in `services` variable.
```bash
ansible-playbook -i hosts/<your-host-file> playbook.yml # remote installation
ansible-playbook --ask-become-pass -i hosts/local playbook.yml # local installation
```
or you can specify explicitly services that you want to install/update
```bash
ansible-playbook -i hosts/<your-host-file> -e install=wordpress playbook.yml
```

You can run installation multiple times. It should only check that everything is
setup correctly. If you'd increase version in software in your hosts file or we
would increase the version in the underlying roles than running the installation
again would update the application.


