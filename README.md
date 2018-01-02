nagios_check_lvm_ThinPool
=========================

check usage of lvm ThinPool

# Usage
``` shell
check_lvm_ThinPool [options] vg_name lv_name
Options:
    -w,--warning <percent>     define usage warning percent (between 0-100, default: 80)
    -c,--critical <percent>    define usage critical percent (between 0-100, default: 100)
    -h,--help                print this help
```

# Example to add this in nrpe
 + Copie this script in `/usr/lib/nagios/plugins` directory
 + Add nagios in sudoers to execute this script:
``` shell
nagios  ALL=(ALL) NOPASSWD:/usr/lib/nagios/plugins/check_lvm_ThinPool
```
 + Create file `/etc/nagios/nrpe.d/check_lvm_ThinPool.cfg` with this content:
``` shell
command[check_lvm_ThinPool]=sudo /usr/lib/nagios/plugins/check_lvm_ThinPool <vg_name> <lv_ThinPoolName>
```
 + Reload NRPE service
``` shell
systemctl reload nagios-nrpe-server.service
```
