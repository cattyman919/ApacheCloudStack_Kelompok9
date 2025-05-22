# ☁️ Apache CloudStack - Kelompok 9

Nama Kelompok :

- Seno Pamungkas Rahman (2106731586)
- Jeffri (2106705070)
- Daniel Niko Mardjaja (2206026183)
- Ivander Andreas Wijaya (2206031896)
- Faruq Sami Ramadhan (2206026675)

Proyek ini bertujuan untuk membangun private cloud menggunakan [Apache CloudStack](https://cloudstack.apache.org/).
Dokumentasi ini mencakup tahapan instalasi dan konfigurasi lengkap, mulai dari setup jaringan hingga peluncuran instance ISO.

## 📚 Struktur Dokumentasi

- `docs/network-setup.md`: Konfigurasi jaringan dan bridge Linux
- `docs/management-server.md`: Instalasi CloudStack Management Server
- `docs/nfs-setup.md`: Setup NFS untuk primary & secondary storage
- `docs/enable-ssh-root-access.md`: Mengaktifkan akses root via SSH untuk manajemen host KVM
- `docs/cloudstack-setup.md`: Setup Apache CloudStack
- `docs/iso-instance-setup.md`: Instalasi ISO dan peluncuran instance
- `docs/database-setup.md`: Instalasi dan konfigurasi MySQL

## ⚙️ Tools & OS

- OS: Ubuntu Server 24.04
- Hypervisor: KVM
- Cloud Management: Apache CloudStack 4.20
- Network: Private (192.168.1.0/24)

## 🚀 Getting Started

```bash
sudo apt update
sudo apt install cloudstack-management mysql-server nfs-kernel-server
```
