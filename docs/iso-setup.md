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

## Launch Instance from ISO

1. Add Instance
   
   Go to instances on the left side of the dashboard and click add instance.
   ![Instance Direction](/image/image_instance.png)

2. Make Instance with ISO
   ![Add Instance](/image/image_add_instance1.png)
   ![Add Instance](/image/image_add_instance2.png)
   ![Add Instance](/image/image_add_instance3.png)
   ![Add Instance](/image/image_add_instance4.png)

3. Launch Instance

## Install ISO Instance (Debian)

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
