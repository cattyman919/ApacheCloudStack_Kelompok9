# Management Server Setup

## Tambahkan repository:

```bash
echo 'deb https://download.cloudstack.org/ubuntu focal 4.20' | sudo tee /etc/apt/sources.list.d/cloudstack.list
wget -O - https://download.cloudstack.org/release.asc | sudo tee /etc/apt/trusted.gpg.d/cloudstack.asc
sudo apt update
```

## Instalasi CloudStack:

```bash
sudo apt install cloudstack-management
```

