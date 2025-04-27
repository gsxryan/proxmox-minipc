# proxmox-minipc
Adventures with setting up generic imported MiniPCs with Proxmox and integrated GPU passthrough

Generic advertised name for NAS MiniPC:  Bay NAS Mini PC 3 N355, 4 x NVMe Micro NAS Server for Home Desktop Computer, Dual i226-V 2.5GbE LAN Small NAS PC with USB3.2, Support WiFi7/BT5.4, Barebone NO RAM/SSD/OS 
https://www.amazon.com/SJRC-Server-Desktop-Computer-Support/dp/B0DZNVN11R?gQT=2&th=1
![image](https://github.com/user-attachments/assets/fbe8e9c7-5e32-4e3d-883b-4c9a71e89fa5)

IOMMU is stated as 'Supported' in BIOS, however, was not recognized with the IOMMU detect command.
```dmesg | grep -e DMAR -e IOMMU -e AMD-Vi```
MISSING: ```DMAR: IOMMU enabled```

Default shipped operating versions:
![image](https://github.com/user-attachments/assets/0692b866-1bda-4dd9-b49c-093ae8b4529b)

After reviewing BIOS, the following changes were made to activate IOMMU.

Review: IOMMU: supported Under the Chipset tab
![image](https://github.com/user-attachments/assets/1121bfb6-c809-4d9e-b9e2-84cc633512e4)

Graphics Configuration: Primary Display: IGFX
![image](https://github.com/user-attachments/assets/3ebc4574-080c-439d-aeec-6ca1344a145c)

It's now required to switch the HDMI physical input to the second port (on the bottom) in order to retain any physical video output
