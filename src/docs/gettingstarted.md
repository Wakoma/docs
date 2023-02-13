# Getting Started

Welcome to Lokal. This guide is a quick getting started guide and will help you get up and running with the services of your choice.

## Prerequisites

- Fresh install of Ubuntu/Debian based OS on the target machine  
(we test and validate with the latest Ubuntu Server LTS)
- Ansible installed on your local machine
- SSH access with the key of your local machine already configured on the target machine
- Internet access

## Installation

### 1. Clone the repo

Clone the repository to your local machine:
`git clone https://github.com/Wakoma/Lokal.git`

### 2. Set up a hosts file for the services

Determine which services you want installed. Once you are ready, create a new file in the `hosts` folder with the following content. This file contains all variables required to get up and running with all currently supported services:

```yaml
all:
  hosts: "1.2.3.4"
  vars:
    ssl_use_acme: false
    domain: lokal.network
    email_admin: admin@example.com
    password_admin: strong-password
    lokal_secret: nui3fhAoiSDUndakd12
    ansible_user: lokal
    services:
        - base
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
Now we need to change the hosts file to suit your needs. Let's go through the options:

- `hosts`: Specify the IP address of your target machine here.
- `domain`: The domain that you will use, such as `lokal.network`. All services will be provisioned on a subdomain of the specified domain. Eg: `calibre.lokal.network`
- `ssl_use_acme`: Set this value to `true` if you want to request SSL certificates from an ACME service like [Let's Encrypt](https://letsencrypt.org/). Make sure that your server is connected to the internet and has the appropriate DNS records on your domain. If set to false; you can manually add your certificate and key files in the `/opt/lokal/base/traefik/certs/` directory.
- `email_admin`: Specify the email of the administrator here
- `password_admin`: Specify the administrator password here (no special characters)
- `lokal_secret`: Specify a random alphanumeric string here Eg: `nui3fhAoiSDUndakd12`
- `ansible_user`: The username of a non-root user account with sudo privileges that ansible will use to connect and deploy services to your machine.
- `services`: Modify this section by removing any services that you do not want/need. Please note that the `base`, `auth`, and `common` services are necessary and __must__ be installed; as they provide dependencies for other services.

When you're all done and ready, please save the file with a name of your choice in the hosts folder. In this guide, we will assume the name of this file to be `machine`

### 3. Set up a hosts file for preparing the server

Create a new file in the `hosts` folder with the following content:

```yaml
all:
  hosts: "1.2.3.4"
  vars:
    app_user: lokal
    ansible_user: root
    setup_ssh: true
    ssh_key: pub_key
```
Now we need to change the hosts file to suit your needs. Let's go through the options:

- `hosts`: Specify the IP address of your target machine here.
- `ssh_key`: Specify your SSH public key here.
- Ensure that the `ansible_user` from Step 2 is the same as `app_user` here.

### 4. Run the playbooks

You are now ready to run the playbook which will automatically set up and configure the specified services for first-use.

__4.1. Install all necessary ansible roles.__  
From the root folder of the repo, run: `ansible-galaxy install -r requirements.yml`  
When you are done, you should see successful install messages like this:
![Ansible Galaxy Requirements Install](/assets/getting-started/ansible-galaxy-screenshot.png)

__4.2. Run the ansible playbook to prepare the server.__  
From the root folder of the repo, run: `ansible-playbook --ask-become-pass -i hosts/machine prepare.yml`  
This step will set up the firewall, user accounts & keys and configure docker to prepare the server for Lokal installation.

<iframe width="100%" height="350" src="https://www.youtube.com/embed/WilR6gogLpA" title="Docs - Prepare Playbook Run Through" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

__4.3. Run the ansible playbook to install services.__  
From the root folder of the repo, run: `ansible-playbook --ask-become-pass -i hosts/machine playbook.yml`  
This step will deploy all the services that you have chosen in your hosts file from Step 2.