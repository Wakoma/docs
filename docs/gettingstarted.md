# Getting Started

Welcome to Lokal. This guide is a quick getting started guide and will help you get up and running with the services of your choice.

## Prerequisites

- Fresh install of Ubuntu/Debian based OS on the target machine  
(we test and validate with the latest Ubuntu Server LTS)
- Ansible installed on your local machine
- SSH access with the key of your local machine already configured on the target machine
- Internet access

## Installation

### 1. Preparing the client

Client is the computer that has Ansible installed and contains SSH keys for accessing the server.
Nothing apart ansible and its modules will get installed here.

First, clone the repository to your local machine: `git clone https://github.com/Wakoma/Lokal.git`

Second, install ansible and its requirements. Ansible is a python library for remote server configuration.
We use it to prepare your server (optional) and to install Lokal. You can install Ansible either via your
package manager or pip (preferably into a virtual environment). Ansible is not available on Windows otherwise
than through WSL.

```bash
# after installing ansible, run following to install Lokal's ansible dependencies
ansible-galaxy install -r requirements.yml
```

### 2. OPTIONAL: Preparing the server using Ansible

Your server needs to have `docker` and `docker compose` available for a non-root user of your choice. Additionally, no
firewall apart pure `iptables` must be present. If you'd prefer Ansible do all those steps for you then create a one-off
hosts file and use it together with `prepare.yml` playbook. I usually name the hosts file `root@<your-domain>.yml` and
place in in the `hosts` folder with the following content:

```yaml
all:
  hosts: "<server-IP-address>" # e.g. "1.2.3.4"
  vars:
    ansible_user: root
    app_user: <non-root-user> # e.g. "lokal"
    setup_ssh: true # only if the non-root user does not yet exist
    ssh_key: <pub_key> # e.g. ssh-rsa AAAAB3Nza....
```
You can change the hosts file to suit your needs. Let's go through the options:

- `hosts`: Specify the IP address of your target machine here.
- `ssh_key`: Specify your SSH public key here (content, not path). It will be inserted into non-root-user's `~/.ssh/authorized_keys`
- Make sure that you use your `"<non-root-user>"` is the `ansible_user` in the next step.

Now you are ready to run
```bash
ansible-playbook -i hosts/root@<your-domain>.yml prepare.yml
```

### 3. Set up a hosts file for the services

Now we are going to create a hosts file for Lokal installation. Firstly, determine which services you want installed.
Once you are ready, create a new file in the `hosts` folder (I name it `<your-domain>.yml`) with the following content.

```yaml
all:
  hosts: "<server-IP-address>" # e.g. "1.2.3.4"
  vars:
    ssl_use_acme: true
    domain: <your-domain>  # e.g. lokal.network
    email_admin: admin@<your-domain>
    password_admin: "a-strong-password"
    lokal_secret: "someRandomCharactersAndNumbers123"
    ansible_user: <non-root-user> # e.g. "lokal" (app_user from previous step)
    services:
        - azuracast
        - calibre
        - docs
        - ipfs
        - jellyfin
        - jitsi
        - kiwix
        - kolibri
        - matomo
        - moodle
        - netdata
        - nextcloud
        - resourcespace
        - unifi
        - wordpress
```
Now you are ready to start the installation with command
```bash
ansible-playbook -i hosts/<your-domain>.yml playbook.yml
```
We strongly advise to reboot the server after the initial installation or if the initial installation fails.

#### Detailed configuration

We need to change the hosts file to suit your needs. Let's go through the options:

- `hosts`: Specify the IP address of your target machine here.
- `domain`: The domain that you will use, such as `lokal.network`. All services will be provisioned on a subdomain of the specified domain. Eg: `calibre.lokal.network`
- `ssl_use_acme`: Set this value to `true` if you want to request SSL certificates from an ACME service like [Let's Encrypt](https://letsencrypt.org/). Make sure that your server is connected to the internet and your DNS A record points to it. If set to `false`, Lokal will create self-signed certificates or use your key and certificate specified by `ssl_key` and `ssl_cert` - those variables should contain path to your key/cert relative to the CWD when running the playbook.
- `email_admin`: Specify the email of the administrator here
- `password_admin`: Specify the administrator password here (no special characters, no spaces, at least 12 characters)
- `lokal_secret`: Specify a random alphanumeric string here Eg: `nui3fhAoiSDUndakd12`
- `ansible_user`: The username of a non-root user account with sudo privileges that ansible will use to connect and deploy services to your machine.
- `services`: Modify this section by removing any services that you do not want/need. Please do not add `base` and `lokal` because they have specific role.

(optional settings)
- `project_root`: Absolute path (optionally using envvars such as `$HOME`) to where Lokal will be installed (default: /opt/lokal/).

If you are interesed in more details about installation process please refer to a separate [installation site](installation.md)

#### Video of successful preparation

<iframe width="100%" height="350" src="https://www.youtube.com/embed/WilR6gogLpA" title="Docs - Prepare Playbook Run Through" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
