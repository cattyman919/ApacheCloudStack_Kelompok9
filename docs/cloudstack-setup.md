# CloudStack Setup

In this explanation, we will show how you can setup an advanced zone (without security groups) in a typical (home/private) 192.168.1.0/24 network.

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