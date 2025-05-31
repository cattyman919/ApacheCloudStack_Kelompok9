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
   echo 'remote_mode="legacy"' >> /etc/libvirt/libvirtd.conf
   ```

5. **Configure default libvitd config:**

   - Edit `etc/libvirt/libvirtd.conf` as follows:

   ```bash
   echo 'listen_tls=0' >> /etc/libvirt/libvirtd.conf
   echo 'listen_tcp=0' >> /etc/libvirt/libvirtd.conf
   echo 'tcp_port = 16509' >> /etc/libvirt/libvirtd.conf
   echo 'tcp_port = 16514' >> /etc/libvirt/libvirtd.conf
   echo 'mdns_adv = 0' >> /etc/libvirt/libvirtd.conf
   echo 'auth_tcp = "none"' >> /etc/libvirt/libvirtd.conf
   systemctl restart libvirtd
   ```

   Note: the above default libvirtd configuration is just for initial setup. When we add KVM host in CloudStack by default the host will be configured to use more secure TLS configuration.

By following these steps, you can install the KVM module as part of setting up Apache CloudStack for your CloudStack instance.
