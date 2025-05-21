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
