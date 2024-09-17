# pihole raspberry pi ansible role

Role installs and configures Pihole https://pi-hole.net/ with unattentded setup

Documents relating to pi-hole are at https://docs.pi-hole.net/

## Requirements
User with sudo access to the machine.

other [prerequisites](https://docs.pi-hole.net/main/prerequisites/)

### Required Variables
#### pihole_setupvars_webpassword
You will need to generate a admin password for the `pihole_setupvars_webpassword` variable, password is hashed with sha256 twice. You can genearte a password with the following shell command.

With the password in a file (recommended).
```shell
echo -n $(cat ~/piholepass.word) | sha256sum | awk '{printf "%s", $1}' | sha256sum
```

With the password in shell command (not recommended)
```shell
echo -n notsosecretpassword | sha256sum | awk '{printf "%s", $1}' | sha256sum
```

recommended to store this variable in ansible vault.

#### pihole_setupvars_ipv4_address
IPv4 adress of the pihole

#### pihole_setupvars_pihole_dns
List of DNS servers you want the pihole to use

##### DNS
Alternative DNS Providers
```yaml
Google: https://developers.google.com/speed/public-dns
  - 8.8.8.8
  - 8.8.4.4

OpenDNS: https://use.opendns.com/
  - 208.67.222.222
  - 208.67.220.220

Cloudflare: https://1.1.1.1/dns/
  - 1.1.1.1
  - 1.0.0.1
```

## Managing Gravity lists:

This role supports management of Gravity lists. For the role to insert and update these, the gravity management has to be enabled using

```yaml
pihole_gravity_managed: true
```

The Gravity items have to be provided as list with the items `address`, `comment` and optionally a `enabled` state:

```yaml
pihole_gravity_lists:
    - address: https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts
      comment: Default Pi-Hole block list
      enabled: true
```

## Role Variables
[defaults/main.yml](defaults/main.yml) for default values

### Black- or Whitelisting Domains

Domains, wildcards or regex can be added to the PiHole configuration by the following configuration variables. Each of them is a list, expecting a single domain or regular expression per line.

```yaml
pihole_whitelist_domains: []
pihole_blacklist_domains:
  - ads.example.com
pihole_whitelist_regex: []
pihole_blacklist_regex: []
pihole_whitelist_wildcards: []
pihole_blacklist_wildcards: []
```

### Local DNS custom records

To add records to the local DNS of pihole set the `pihole_custom_domains` dict with IP address as key and domain as value
```
pihole_custom_domains:
  1.2.3.4: example.com
  192.168.1.1: my-awesome-router.localdomain
```

## Example Playbook
```yaml
---
- name: pihole
  hosts: pi
  strategy: free
  become_method: sudo
  become: yes
  gather_facts: yes
  vars:
    pihole_setupvars_ipv4_address: 192.168.1.100
    pihole_setupvars_webpassword: 35030714f1136486a612d7014b739a6c7ef3be589bb14b14a3d01f521dd7ef18
    pihole_setupvars_pihole_dns:
      - 1.1.1.1
      - 1.0.0.1
  roles:
    - zfuller.pihole
```
TODO
