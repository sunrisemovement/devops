# This code is unmaintained
This repo is obsolete. A maintained version of this code lives at https://github.com/maximum-ethics/linode-caddy

## Purpose

Ansible role that will (eventually) automatically set up a web server for [Sunrise Movement](https://www.sunrisemovement.org/), currently using a virtual server on [Linode](https://www.linode.com/).

## Steps to set up server on Linode
First, ask Nelson for an account on the Sunrise Linode, so that you can create an API token. Then:

### Create an inventory file for Ansible

Make an inventory file at '/etc/ansible/hosts' (or /usr/local/etc/ansible/hosts if you use homebrew on Mac to install Ansible) with the following contents:
```
[sunrise]
attenborough

[local]
localhost

[local:vars]
pass_attenborough=[get the pass over Signal (for now)]
ssh_pub_key=[PASTE YOUR OWN PUBLIC KEY]
sunrise_linode_token=[PASTE YOUR OWN LINODE API TOKEN]
```

### Create the Linode server from your computer by connecting to the Linode web API
`ansible-playbook linode_create.yml --ask-become-pass`
### Change your password from our default, so you can log in as yourself (not root)
`ssh attenborough`

Change your password when prompted, and then you will be automatically logged out.

### Set up the server
`ansible-playbook sunrise.yml`

This includes setting up the [Caddy v2 web server](https://caddyserver.com/), serving some hand-coded hub websites.

**WAIT!** If you are changing the IP address for an existing domain name, consider the TTL! [Linode's default TTL is 24 hours](https://www.linode.com/docs/platform/manager/dns-manager/#troubleshoot-dns-records). Many of our Godaddy subdomains currently have the TTL set to 1 hour.

If you power up your new Caddy webserver before DNS resolves correctly, it won't be able to get certificates from Let's Encrypt, and won't serve your websites. Worse, you may annoy Let's Encrypt by exceeding their rate limits (requesting certs you can't get) and then you'll have to wait even longer! (In the future we'll set up a dev environment so that you can test this playbook without requesting certs from Let's Encrypt.)

Make sure the domain resolves to the correct IP address before setting up Caddy.

To set up the server but avoid setting up Caddy, try:

`ansible-playbook sunrise.yml --skip-tags "caddy_v2,new_handcoded"`
