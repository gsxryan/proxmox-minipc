# proxmox-minipc
Adventures with setting up generic imported MiniPCs with Proxmox and integrated GPU passthrough
This specific model is 12th gen alder lake.  Intel doesn't appear to still support GVT for this processor, but as configured below, it still appears to work, however, without displaying the graphics type in proxmox GUI.
**https://www.intel.com/content/www/us/en/support/articles/000093216/graphics/processor-graphics.html**

Generic advertised name for NAS MiniPC:  Bay NAS Mini PC 3 N355, 4 x NVMe Micro NAS Server for Home Desktop Computer, Dual i226-V 2.5GbE LAN Small NAS PC with USB3.2, Support WiFi7/BT5.4, Barebone NO RAM/SSD/OS 
https://www.amazon.com/SJRC-Server-Desktop-Computer-Support/dp/B0DZNVN11R?gQT=2&th=1
![image](https://github.com/user-attachments/assets/fbe8e9c7-5e32-4e3d-883b-4c9a71e89fa5)

IOMMU is stated as 'Supported' in BIOS, however, was not recognized with the IOMMU detect command.
```dmesg | grep -e DMAR -e IOMMU -e AMD-Vi```
MISSING: ```DMAR: IOMMU enabled```
NOTE: It may be enabled, even if this message does not appear exactly.  verify there are iommu groups with:
```for d in /sys/kernel/iommu_groups/*/devices/*; do n=${d#*/iommu_groups/*}; n=${n%%/*}; printf 'IOMMU group %s ' "$n"; lspci -nnks "${d##*/}"; done```


** Cliffnotes:
*** Setup

   nano /etc/default/grub
     intel_iommu=on iommu=pt i915.enable_gvt=1
    
    6  update-grub2
    7  nano /etc/modprobe.d/pve-blacklist.conf
    8  nano /etc/modprobe.d/vfio.conf
    9  nano /etc/modules
   11  nano /etc/kernel/cmdline
   12  update-initramfs -u -k all
   13  dmesg | grep -e DMAR -e IOMMU -e AMD-Vi
   14  for d in /sys/kernel/iommu_groups/*/devices/*; do n=${d#*/iommu_groups/*}; n=${n%%/*}; printf 'IOMMU group %s ' "$n"; lspci -nnks "${d##*/}"; done
   16  dmesg | grep 'remapping'
   17  pvesh get /nodes/pve1/hardware/pci --pci-class-blacklist ""
   18  lspci -nn
   19  lsmod | grep vfio
   20  lspci -k | grep -A 3 "VGA"
   23  qm set 100 -hostpci0 00:02.0

Verify: Card0
ls -l /dev/dri
   
Default shipped operating versions:
![image](https://github.com/user-attachments/assets/0692b866-1bda-4dd9-b49c-093ae8b4529b)

After reviewing BIOS, the following changes were made to activate IOMMU.

Review: IOMMU: supported Under the Chipset tab
![image](https://github.com/user-attachments/assets/1121bfb6-c809-4d9e-b9e2-84cc633512e4)

Graphics Configuration: Primary Display: IGFX
![image](https://github.com/user-attachments/assets/3ebc4574-080c-439d-aeec-6ca1344a145c)

It's now required to switch the HDMI physical input to the second port (on the bottom) in order to retain any physical video output

Disable ME firmware:
![image](https://github.com/user-attachments/assets/be7772c6-f45e-4569-9f7e-89caf982e8c9)

Restore Power on AC loss:
![image](https://github.com/user-attachments/assets/3295f11f-7c5f-43eb-97e2-9d30f5c305f6)

