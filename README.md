# ☁️ Apache CloudStack - Kelompok 9

<p align="center">
  <img src="/image/image_2025-06-04_212812320.png" alt="Logo Departemen Teknik Elektro FTUI" width="1000"/>
</p>
<p align="center">
  <strong>Departemen Teknik Elektro - Fakultas Teknik Universitas Indonesia</strong>
</p>

Anggota Kelompok :

- Seno Pamungkas Rahman (2106731586)
- Jeffri (2106705070)
- Daniel Niko Mardjaja (2206026183)
- Ivander Andreas Wijaya (2206031896)
- Faruq Sami Ramadhan (2206026675)

Proyek ini bertujuan untuk membangun private cloud menggunakan [Apache CloudStack](https://cloudstack.apache.org/).
Dokumentasi ini mencakup tahapan instalasi dan konfigurasi lengkap, mulai dari setup jaringan hingga peluncuran instance ISO.

## Table of Contents
- [📚 Struktur Dokumentasi](#📚-struktur-dokumentasi)
- [🎬 Video Guideline](#🎬-video-guideline)
- [⚙️ Tools & OS](#⚙️-tools--os)
- [🚀 Getting Started](#🚀-getting-started)

---

## 📚 Struktur Dokumentasi

Dokumentasi ini telah dirapikan dan disusun dalam satu file README. Klik tautan di bawah untuk langsung menuju bagian terkait:

1. [Network Setup](#network-setup) — Konfigurasi jaringan dan bridge Linux
2. [Enable SSH Root Access](#enable-ssh-root-access) — Mengaktifkan akses root via SSH untuk manajemen host KVM
3. [Management Server Setup](#management-server-setup) — Instalasi CloudStack Management Server
4. [Installing MySQL Database](#installing-mysql-database-in-cloudstack-environment) — Instalasi dan konfigurasi MySQL
5. [NFS Setup](#nfs-setup) — Setup NFS untuk primary & secondary storage
6. [Installing KVM Module](#installing-kvm-module-for-apache-cloudstack) — Setup host KVM dan konfigurasi libvirt
7. [CloudStack Setup](#cloudstack-setup) — Setup Apache CloudStack (zona, jaringan, storage)
8. [ISO Setup](#iso-setup) — Menambahkan ISO ke CloudStack
9. [Launch Instance from ISO](#launch-instance-from-iso) — Peluncuran instance dari ISO
10. [Tailscale VPN Setup](#tailscale-vpn-setup-for-apache-cloudstack) — Akses private CloudStack dari jaringan VPN

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

- **OS**: Ubuntu Server 24.04 🐧
- **Hypervisor**: KVM
- **Cloud Management**: Apache CloudStack 4.20 ☁️
- **Network**: Private (192.168.1.0/24) 🌐

---

## 🚀 Getting Started

```bash
sudo apt update
sudo apt install cloudstack-management mysql-server nfs-kernel-server
```


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
![netplan config](https://github.com/cattyman919/ApacheCloudStack_Kelompok9/blob/main/image/image_netplan.png)

Jalankan:
```bash
netplan generate
netplan apply
reboot
```

# Enable SSH Root Access

SSH is a cryptographic network protocol that enables secure communication between clients and servers over untrusted networks. SSH uses encryption to protect the data being sent, including authentication, commands, and file transfers.

1. KVM Host Management

- CloudStack Management Server needs to fully control the KVM host to:

    - Create/delete VMs

    - Manage the virtual network

    - Monitor resources (CPU, RAM, storage)

- Without root access, CloudStack Agent cannot perform administrative tasks.

2. Automatic Deployment and Configuration

- CloudStack uses SSH to:

    - Install and configure agents on KVM hosts

    - Modify configuration files (such as libvirtd, qemu, and network)

3. Communication between Management Server and Hosts

- CloudStack Management Server communicates with the KVM host via SSH to:

    - Retrieve performance statistics

    - Executing hypervisor commands (e.g. virsh)


## Detailed steps
1. Editing the SSH configuration file:
- Open the /etc/ssh/sshd_config file with a text editor (nano, vim, etc)
- Look for the line containing PermitRootLogin
- If there is a # sign in front of it, remove it (uncomment)
- Change the value to yes so that it becomes:

```
PermitRootLogin yes
```
![Alt text](/image/image_SSH.png)

2. Restart the SSH service:

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

# Installing MySQL Database in CloudStack Environment

To set up MySQL database in your CloudStack environment, follow these steps:

1.  **Update Package Repository:**

    ```bash
    sudo apt update
    ```

2.  **Install MySQL Server:**

    ```bash
    sudo apt install mysql-server
    ```

3.  **Open MySQL Configuration File:**
    Open MySQL configuration file on `/etc/mysql/mysql.conf.d/cloudstack.cnf` and make necessary changes if required depending on OS and installation location. Then, insert the following lines in the `mysqld` section:

    ```cnf
    [mysqld]
    innodb_rollback_on_timeout=1
    innodb_lock_wait_timeout=600
    max_connections=350
    log-bin=mysql-bin
    binlog-format = 'ROW'
    ```

4.  **Restart MySQL Services:**

    ```bash
    sudo systemctl restart mysql
    ```

5.  **Setup Database:**

    ```bash
    cloudstack-setup-databases cloud:cloud@localhost --deploy-as=root:<root password, or leave default blank if MySQL server has no root password> -i <cloudbr0 IP here>
    ```

Now you have successfully installed MySQL database in your CloudStack environment.

# NFS Setup

NFS is a distributed file system protocol that allows systems to share directories and files over a network. NFS works in a client-server model, where the server exports directories, and clients mount them to access them.

## The Role of NFS in CloudStack

1. Primary Storage

- Stores the virtual disk (volume) of the VM.

- Used for:

    - VM root disk storage (/dev/vda)

    - Additional disk data (/dev/vdb, etc.)

- Must be high-performance as it affects the speed of the VM.

2. Secondary Storage

- Save:

    - OS templates (e.g. Debian, Ubuntu, CentOS)

    - ISO images (for VM installation)

    - Snapshots (backup of VM state)

- Doesn't need to be as fast as primary storage, but should have a large capacity.

## Detailed steps 
1. NFS Package Installation:

```bash
sudo apt install nfs-kernel-server
```

2. Directory Preparation 

Create directories for primary and secondary storage:

```bash
sudo mkdir -p /export/primary
sudo mkdir -p /export/secondary
```

Set the right permission:

```bash
sudo chown -R nobody:nogroup /export
sudo chmod -R 777 /export
```

3. Configure Exports NFS:

Edit file /etc/exports and add :

```
/export *(rw,async,no_root_squash,no_subtree_check)
```

Command Explanation:

- rw: read and write

- async: performance enhancement

- no_root_squash: allow root access from client

- no_subtree_check: improved reliability


4. Activate Export:

```bash
sudo exportfs -a
```

5. Start dan Enable Service:

```bash
sudo systemctl start nfs-server
sudo systemctl enable nfs-server
```

# Installing KVM Module for Apache CloudStack

To install the KVM module as part of setting up Apache CloudStack for your CloudStack instance, follow these steps:

1. **Install KVM Hypervisor:**

   - Ensure your system supports hardware virtualization.
   - Install the required packages for KVM and CloudStack agent. You can do this using the package manager of your Linux distribution. For example, on Ubuntu, you can run:

     ```bash
     sudo apt-get install qemu-kvm cloudstack-agent
     ```

2. **Configure Libvirtd:**

   - Edit the Libvirtd configuration file `/etc/default/libvirtd` to enable VNC for console proxy.
   - For Ubuntu 22.04, use this configuration:
     ```bash
     echo LIBVIRTD_ARGS=\"--listen\" >> /etc/default/libvirtd
     ```

   - For Ubuntu 20.04 and later, the traditional socket/listen based configuration may not be supported, but we could get the old behaviour using:
     ```bash
     systemctl mask libvirtd.socket libvirtd-ro.socket libvirtd-admin.socket libvirtd-tls.socket libvirtd-tcp.socket
     ```

4. **Configure Libvirt:**

   - Connect Libvirt to Libvirtd, not to the daemons
   - Important on newer distros, such as Ubuntu 24.04 and EL9
   - Edit `etc/libvirt/libvirt.conf` and add the following:

   ```bash
   echo 'remote_mode="legacy"' >> /etc/libvirt/libvirt.conf
   ```

5. **Configure default libvitd config:**

   - Edit `etc/libvirt/libvirtd.conf` as follows:

   ```bash
   echo 'listen_tls=0' >> /etc/libvirt/libvirtd.conf
   echo 'listen_tcp=0' >> /etc/libvirt/libvirtd.conf
   echo 'tcp_port = "16509"' >> /etc/libvirt/libvirtd.conf
   echo 'tls_port = "16514"' >> /etc/libvirt/libvirtd.conf
   echo 'mdns_adv = 0' >> /etc/libvirt/libvirtd.conf
   echo 'auth_tcp = "none"' >> /etc/libvirt/libvirtd.conf
   systemctl restart libvirtd
   ```

   Note: the above default libvirtd configuration is just for initial setup. When we add KVM host in CloudStack by default the host will be configured to use more secure TLS configuration.

By following these steps, you can install the KVM module as part of setting up Apache CloudStack for your CloudStack instance.

# CloudStack Setup

In this explanation, we will show how you can setup an advanced zone (without security groups) in a typical (home/private) 192.168.1.0/24 network.

## Video Guide

[![Apache Cloudstack Setup](https://img.youtube.com/vi/i7VBEj6q27o/0.jpg)](https://youtu.be/i7VBEj6q27o)

## 1. Setup Advanced Zone Details
To set up our CloudStack, we have to proceed to http://192.168.1.225(i.e. the cloudbr0-IP):8080/client first, then log in using the default credentials - username `admin` and password `password`.

![log in](/image/image_login_cloudstack.png)

Once we were logged in, go to Infrastructure > Zone and click on add zone button, select advanced zone and provide following configuration:
```
Name - <any name>
Public DNS 1 - 8.8.8.8
Public DNS 2 - 8.8.4.4
Internal DNS1 - 192.168.1.1
Hypervisor - KVM
Network offering: Offering for Shared Security group enabled networks
```

![setup zone 1](/image/image_setup_zone_1.png)
![setup zone 2](/image/image_setup_zone_2.png)
![setup zone 3](/image/image_setup_zone_3.png)
![setup zone 4](/image/image_setup_zone_4.png)
![setup zone 5](/image/image_setup_zone_5.png)

## 2. Setup Zone Network
Once we have established the zone's details, we can continue to setting up the networks. During this step, we will setup 4 different parts of the network, the physical network, the public traffic, the pod, and the guest traffic. 

In the physical network configuration, use the default settings, where VLAN is the isolation method on a single physical nic (on the host) that will carry all traffic types (management, public, guest etc).

![setup physical network 1](/image/image_physical_network.png)

Next, provide the following configuration for the public traffic:
```
Gateway - 192.168.1.1
Netmask - 255.255.255.0
Start/end reserved system IPs - 192.168.1.50 - 192.168.1.60
```
![setup public traffic 1](/image/image_public_traffic_1.png)
![setup public traffic 2](/image/image_public_traffic_2.png)

Then, provide the following configuration for the pod:
```
Name - <any name>
Reserved system gateway - 192.168.1.1
Reserved system netmask - 255.255.255.0
Start/end reserved system IPs - 192.168.1.61 - 192.168.1.70
```

![setup pod](/image/image_pod.png)

Finally, provide the following configuration for the guest traffic:
```
VLAN-VNI range 700 - 900
```

![setup guest traffic](/image/image_guest_traffic.png)

## 3. Add Resources
After configuring the network, we setup the resources, starting with creating a cluster as follows:

![create cluster](/image/image_cluster.png)

Next, add the default/first host with the following configuration:
```
Hostname - 192.168.1.225
Username - root
Password - <password for root user, please enable root user ssh-access by password on the KVM host> (‘cloud’ is our group 9 password)
```

![create first host](/image/image_first_host.png)

Now, add the primary storage with the following configuration:
```
Name - <any name>
Scope - Zone
Protocol - NFS
Server - 192.168.1.225
Path - /export/primary
```

![create primary storage](/image/image_primary_storage.png)

Then, add the secondary storage with the following configuration:
```
Provider - NFS
Name - <any name>
Server - 192.168.1.225
Path - /export/secondary
```

![create secondary storage](/image/image_secondary_storage.png)

Next, click the `Launch Zone` button and the `Enable Zone` button.

![launch zone](/image/image_launch_zone.png)
![enable zone](/image/image_enable_zone.png)

## 4. Verify Components
Lastly, verify that all the components is up and running.

![verify components](/image/image_verify_components.png)

## 5. Network Configuration
To finish it off, we can also change a few configurations for the CloudStack network, such as adding a new network or adding a new egress rules.

### Adding new network

![add new network](/image/image_new_network.png)

Note: Change the gateway for the network to any IP address gateway different from the physical IP network used in earlier steps.

### Add an egress rule
Egress rule is a rule which defines the criteria for allowing outbound packets from a network boundary into outwards. By adding an egress rule, this essentially enables the VM to send packets to the Internet.

![add new egress rule](/image/image_egress.png)


# ISO Setup

ISO stands for the International Organization for Standardization. It is an independent, non-governmental international body that develops and publishes global standards to ensure the quality, safety, efficiency, and interoperability of products, services, and systems across a wide range of industries—including cloud computing.

## How to add Iso in CloudStack

1. Add Iso
   
   Open CloudStack Management, then on the dashboard on the left select ISOs and click Register ISO.
   ![ISO Direction](/image/image_iso1.png)
   ![Direction](/image/image_iso2.png)

2. Select Register ISO
   
   For example we want to add Debian Server 12, we can find the downloadable iso in this link (https://kartolo.sby.datautama.net.id/debian-cd/current/amd64/iso-dvd/debian-12.10.0-amd64-DVD-1.iso). Fill in as shown below then click ok.
   ![Register ISO](/image/image_register_iso.png)

3. ISO Download and Ready Status
   
   Debian Server will be ready when the ISO has finished downloading
   ![Downloading ISO](/image/image_downloading_iso.png) 


# Launch Instance from ISO

1. Add Instance
   
   Go to instances on the left side of the dashboard and click add instance.
   ![Instance Direction](/image/image_instance.png)

2. Make Instance with ISO
   ![Add Instance](/image/image_add_instance1.png)
   ![Add Instance](/image/image_add_instance2.png)
   ![Add Instance](/image/image_add_instance3.png)
   ![Add Instance](/image/image_add_instance4.png)

3. Launch Instance

# Install ISO Instance (Debian)

1. Open console proxy
   ![Select Instance](/image/image_new_instance.png)
   ![Console Proxy Guide](/image/image_console_proxy.png)

2. Follow the typical debian server installation from here
   ![Debian](/image/image_debian1.png)
   ![Debian](/image/image_debian2.png)
   ![Debian](/image/image_debian3.png)
   ![Debian](/image/image_debian4.png)

3. After finished installing, detach the ISO from the instance
   ![detach Guide](/image/image_detach_guide.png)
   ![detach ISO](/image/image_detach_iso.png)

4. Reboot the instance and go back to the console proxy
   ![Debian Server](/image/image_debian_server.png)
   We have successfully installed debian server on apache cloudstack


# Tailscale VPN Setup for Apache CloudStack

This document outlines the steps to install and configure Tailscale VPN, enabling users to deploy Apache CloudStack on a private network accessible from anywhere, even without public IP addresses.

Tailscale VPN is a mesh VPN that simplifies secure access to your networked devices. It allows you to connect your devices directly without the need for complex firewall rules or port forwarding.

## Prerequisites

- A running instance of Apache CloudStack.
- Access to the terminal on the server where Apache CloudStack is installed.
- A Tailscale account.

## Installation Steps

### Step 1: Install Tailscale

1. **Download and Install Tailscale**  
   For Debian/Ubuntu systems, run:

   ```bash
   curl -fsSL https://tailscale.com/install.sh | sh
   ```

   For CentOS/RHEL systems, run:

   ```bash
   sudo yum install tailscale
   ```

2. **Start Tailscale**  
   After installation, start the Tailscale service:
   ```bash
   sudo tailscale up
   ```

### Step 2: Authenticate Tailscale

1. **Log in to Tailscale**  
   Follow the on-screen instructions to authenticate your Tailscale account. This will link your server to your Tailscale network.

### Step 3: Configure Apache CloudStack

1. **Access Apache CloudStack**  
   Ensure that your Apache CloudStack management server is running and accessible.

2. **Update Network Settings**  
   Configure your CloudStack to use the Tailscale IP addresses for communication between instances.

### Step 4: Verify Connectivity

1. **Check Tailscale Status**  
   Verify that Tailscale is running and connected:

   ```bash
   tailscale status
   ```

2. **Test Access**  
   From another device connected to your Tailscale network, try to access your Apache CloudStack instance using its Tailscale IP address.

## Conclusion

By following these steps, you can successfully set up Tailscale VPN to allow access to your Apache CloudStack deployment over a private network. This setup is particularly useful for environments without public IP addresses.

For further information, refer to the [Tailscale documentation](https://tailscale.com/kb/).
