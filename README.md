# vfio-setup-libvirt-xml


## Error reading input/Permission Denied:

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

`echo 1 > /sys/module/kvm/parameters/ignore_msrs`

To make it permanently you can create a modprobe file `kvm.conf`:

`options kvm ignore_msrs=1`

