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
![Alt text](/Image/image_SSH.png)

2. Restart the SSH service:

```bash
sudo systemctl restart ssh
```