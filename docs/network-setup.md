# Network Setup

Kami menggunakan bridge `cloudbr0` untuk seluruh trafik (management, guest, public, storage).

## 1. Install bridge-utils

Setup Linux bridges that will handle CloudStack’s public, guest, management and storage traffic. For simplicity, we will use a single bridge cloudbr0 to be used for all these networks. Install bridge utilities:

```bash
apt-get install bridge-utils
```
This guide assumes that you’re in a 192.168.1.0/24 network which is a typical RFC1918 private network.

## 2. Konfigurasi Netplan (`/etc/netplan/50-cloud-init.yaml`)

Admins can use netplan to configure networking. The default installation creates a file at /etc/netplan/50-cloud-init.yaml that you should comment applying your network and interface/name specific changes:

```yaml
ethernets:
  enp1s0:
    dhcp4: false
    dhcp6: false
    optional: true

vlans:
  enp1s0.200:
    id: 200
    link: enp1s0
    dhcp4: false
    dhcp6: false

bridges:
  cloudbr0:
    addresses: [192.168.1.225/24]
    routes:
      - to: default
        via: 192.168.1.1
    nameservers:
      addresses: [1.1.1.1, 8.8.8.8]
    interfaces: [enp1s0]
    dhcp4: false
    dhcp6: false
    parameters:
      stp: false
      forward-delay: 0

  cloudbr1:
    interfaces: [enp1s0.200]
    dhcp4: false
    dhcp6: false
    parameters:
      stp: false
      forward-delay: 0
```
![netplan config](/image/image_netplan.png)

Jalankan:
```bash
netplan generate
netplan apply
reboot
```

