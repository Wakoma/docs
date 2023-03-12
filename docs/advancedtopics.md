# HTTPS Cert Resolvers

Lokal provides a few ways of providing HTTPS. You need SSL certificates for your
domain to be able to use HTTPS protocol. Lokal uses [traefik](https://traefik.io)
for routing thus we are using its capabilities.

## HTTP challenge

The simplest method for obtaining certificates from Let's Encrypt is using the 
HTTP challenge. Before you use this, you need to prepare your DNS A entries to 
point to your Lokal server. On Lokal side, use this as your [hosts vars](configuration.md).

```yaml
email_admin: your@email.com
ssl_use_acme: true
cert_resolver: http
```

Your email is necessary for registering to Let's encrypt. `server_is_life` turns
on the usage of Let's Encrypt and cert_resolver to http is obvious in this case.

Traefik will generate one certificate for each subdomain that any of your 
application uses. It even handles automatic renewal before expiration.

## DNS challenge

DNS challenge still uses Let's Encrypt but it doesn't require you to pre-set your
DNS records. Since somebody has to do it, then your DNS provider must support API
access and traefik handles this. Currently, the only supported DNS provider is 
Namecheap. Following variables are necessary in the hosts file.

```yaml
email_admin: your@email.com
ssl_use_acme: true
cert_resolver: dns
dns_provider: namecheap
namecheap_api_user: your-user
namecheap_api_key: your-key
```


## Static certificates

Suppose you have set your domain's DNS to your Lokal server IP and you have a
wildcard certificate and matching key for your domain. Simply specify them as
relative from `CWD` where you are running the main playbook from.

```yaml
ssl_cert: "hosts/<my-domain>.crt"
ssl_key: "hosts/<my-domain>.key"
```

## Self-signed certificates

If you don't setup `ssl_use_acme` nor `ssl_key` and `ssl_cert` then this is the 
default option.  The disadvantage is, that your temporal certificate authority
will definitely not be trusted by browsers. Therefore anybody accessing any 
Lokal services will get "Invalid certificate" warning and must confirm security
exception for every domain separately.


# Operations

Operations describe management during the full lifetime of Lokal. In general, you
will want to perform
- upgrade
- backup
- restore
- remove

All of those commands are supported by `playbook.yml` and their support should be
included in every role (service).

## Upgrade

Roles should be written in such ways that the installation should behave as an upgrade
if it is run on already installed service. Usually, there are no extra steps required
than simply downloading a new docker image and restarting the service. Surely, you
want to upgrade services one-by-one, therefore we included `install` variable to
let you do exactly that. Following command will install/upgrade only the specified
services/roles.

```bash
ansible-playbook -i hosts/<your-host-file> -e install=wordpress,matmoto playbook.yml
```

## Backup

You can either backup concrete services by specifying `-e backup=service1,service2`.
```bash
ansible-playbook -i hosts/<your-host-file> -e backup=wordpress,matmoto playbook.yml
```

## Restore

Of course you can restore backed up services using `-e restore=service1,service2`.
```bash
ansible-playbook -i hosts/<your-host-file> -e restore=wordpress playbook.yml
```

## Uninstall

For uninstallation we provide `-e remove=service1,service2` variable.
```bash
ansible-playbook -i hosts/<your-host-file> -e remove=wordpress playbook.yml
```