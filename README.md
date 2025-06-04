# ☁️ Apache CloudStack - Kelompok 9

<p align="center">
  <img src="/image/image_2025-06-04_212812320.png" alt="Logo Departemen Teknik Elektro FTUI" width="600"/>
</p>
<p align="center">
  <strong>Departemen Teknik Elektro - Fakultas Teknik Universitas Indonesia</strong>
</p>

Nama Kelompok :

- Seno Pamungkas Rahman (2106731586)
- Jeffri (2106705070)
- Daniel Niko Mardjaja (2206026183)
- Ivander Andreas Wijaya (2206031896)
- Faruq Sami Ramadhan (2206026675)

Proyek ini bertujuan untuk membangun private cloud menggunakan [Apache CloudStack](https://cloudstack.apache.org/).
Dokumentasi ini mencakup tahapan instalasi dan konfigurasi lengkap, mulai dari setup jaringan hingga peluncuran instance ISO.

---

## 📚 Struktur Dokumentasi

[Wiki Guide](https://github.com/cattyman919/ApacheCloudStack_Kelompok9/wiki)

1. `docs/network-setup.md`: Konfigurasi jaringan dan bridge Linux
2. `docs/enable-ssh-root-access.md`: Mengaktifkan akses root via SSH untuk manajemen host KVM
3. `docs/management-server.md`: Instalasi CloudStack Management Server
4. `docs/database-setup.md`: Instalasi dan konfigurasi MySQL
5. `docs/nfs-setup.md`: Setup NFS untuk primary & secondary storage
6. `docs/kvm-setup.md`: Setup host KVM dan konfigurasi libvirt
7. `docs/cloudstack-setup.md`: Setup Apache CloudStack
8. `docs/iso-setup.md`: Instalasi ISO
9. `docs/launch_instance.md`:  Peluncuran instance

---

## 🎬 Video Guideline

### Apache Cloudstack Installation 
[![Apache Cloudstack Installation](https://img.youtube.com/vi/VlF2fn_pIzA/0.jpg)](https://youtu.be/VlF2fn_pIzA)

### Apache Cloudstack Setup 
[![Apache Cloudstack Setup](https://img.youtube.com/vi/i7VBEj6q27o/0.jpg)](https://youtu.be/i7VBEj6q27o)

### Setup ISO & Launch ISO
[![ISO Setup & Launch Instance From ISO](https://img.youtube.com/vi/E8Pq-wVS8es/0.jpg)](https://youtu.be/E8Pq-wVS8es)

---

## ⚙️ Tools & OS

-   **OS**: Ubuntu Server 24.04 🐧
-   **Hypervisor**: KVM
-   **Cloud Management**: Apache CloudStack 4.20 ☁️
-   **Network**: Private (192.168.1.0/24) 🌐

---

## 🚀 Getting Started

```bash
sudo apt update
sudo apt install cloudstack-management mysql-server nfs-kernel-server
