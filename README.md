# vfio-setup-libvirt-xml

```
/etc/default/grub
GRUB_CMDLINE_LINUX_DEFAULT="amd_iommu=on iommu=pt ..."
```
(Check with ```dmesg | grep -i -e DMAR -e IOMMU``` after reboot)

```
/boot/loader/entries/arch.conf
options ... amd_iommu=on iommu=pt
```
```
/etc/modprobe.d/vfio.conf
options vfio-pci ids=10de:1b80,10de:10f0
```
```
/etc/mkinitcpio.conf
MODULES=(vfio_pci vfio vfio_iommu_type1 vfio_virqfd)
```
```
mkinitcpio -p linux
reboot
```
(Check with ```dmesg | grep -i vfio``` after reboot)

sudo pacman -S qemu libvirt ovmf virt-manager dnsmasq 
sudo systemctl enable libvirtd
sudo systemctl start libvirtd
Virtual Machine Manage starten, File -> Add Connection Qemu/KVM
wget https://raw.githubusercontent.com/peterge1998/vfio-setup-libvirt-xml/master/etc/libvirt/qemu/win10.xml
virsh define win10.xml

## Error reading input/Permission Denied:
[Source](https://www.reddit.com/r/VFIO/comments/cx5gos/permission_denied_when_trying_to_use_my_mouse_in/)

Edit ```/etc/libvirt/qemu.conf```:
```
user = "$USERNAME"
group = "kvm"
```
Replace with your path:
```
cgroup_device_acl = [
    "/dev/kvm",
    "/dev/input/by-id/KEYBOARD_NAME",
    "/dev/input/by-id/MOUSE_NAME",
    "/dev/input/by-id/usb-Logitech_Gaming_Mouse_G502_0E8736563037-event-mouse",
    "/dev/input/by-id/usb-HyperX_Alloy_Elite_RGB_HyperX_Alloy_Elite_RGB-if01-event-kbd",
    "/dev/null", "/dev/full", "/dev/zero",
    "/dev/random", "/dev/urandom",
    "/dev/ptmx", "/dev/kvm", "/dev/kqemu",
    "/dev/rtc","/dev/hpet", "/dev/sev"
]
```
`usermod -a -G input $USERNAME`

`systemctl restart libvirtd`

## VM not booting, just displaying booloader info
[Source](https://wiki.archlinux.org/index.php/PCI_passthrough_via_OVMF#AMD_Ryzen_/_BIOS_updates_(AGESA)_yields_%22Error:_internal_error:_Unknown_PCI_header_type_%E2%80%98127%E2%80%99%22)

`echo 1 > /sys/module/kvm/parameters/ignore_msrs`

To make it permanently you can create a modprobe file `kvm.conf`:

`options kvm ignore_msrs=1`

