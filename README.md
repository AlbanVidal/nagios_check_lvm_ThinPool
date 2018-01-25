nagios check lvm ThinPool
=========================

check usage of lvm ThinPool

# Dependances
`lvs` and `awk` binarys are required, to install on Debian/Ubuntu system:
``` shell
apt install lvm2 gawk
```

# Usage
``` shell
check_lvm_ThinPool [options] vg_name lv_name
Options:
    -w, --warning  <percent>    Define usage warning percent (between 0-100, default: 80)
    -c, --critical <percent>    Define usage critical percent (between 0-100, default: 100)
    -p, --perf-data             Enable performance data output
    -e, --examples              Show usage examples
    -h, --help                  Print this help
```

# Usage examples
``` shell
# Simple check:
check_lvm_ThinPool vg_srv-1 LXDThinpool
    OK - ThinPool vg_srv-1/LXDThinpool: data=63,40% snap=63,40% meta=32,76%

# Change range Waning and Critical:
check_lvm_ThinPool -w 50 -c 60 vg_srv-1 LXDThinpool
CRITICAL - ThinPool vg_srv-1/LXDThinpool: data=63,41% snap=63,41% meta=32,76%
```

# Example to add this in nrpe
 + Copy this script in `/usr/lib/nagios/plugins` directory
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
