nagios check lvm ThinPool
=========================

check usage of lvm ThinPool

# Dependances

## For CLI (nagios) mode
`lvs` and `awk` binarys are required, to install on Debian/Ubuntu system:
``` shell
apt install lvm2 gawk
```

## For GUI mode
In GUI mode, your also need notify-send binary, provides from libnotify-bin package
``` shell
apt install libnotify-bin
```

# Usage
``` shell
check_lvm_ThinPool [options] vg_name lv_name
Options:
    -w, --warning  <percent>    Define usage warning percent (between 0-100, default: 80)
    -c, --critical <percent>    Define usage critical percent (between 0-100, default: 100)
    -p, --perf-data             Enable performance data output
    -g, --gui-notify <user>     Enable GUI notification mode for Desktop usage
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

Desktop usage (GUI notification):
check_lvm_ThinPool --gui-notify alban vg_port lv_ThinPool
    (No CLI output, just in notification area)
    LVM ThinPool Alert
    ThinPool vg_port/lv_ThinPool data=61,03% snap=61,03% meta=20,11%
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

# Desktop mode (GUI notification)
You can add the path of this script in your crontab file or in systemd timers to receive notification in your desktop environment.

Tested in the following environments:
 + [x] Debian 9 + Mate

## GUI mode, timer with Systemd

+ Step 1, create a oneshot service

```shell
cat << EOF > /etc/systemd/system/check-lvm-thinpool.service
[Unit]
Description=Check LVM ThinPool

[Service]
Type=oneshot
ExecStart=/usr/local/bin/check_lvm_ThinPool -g alban vg_port lv_ThinPool
EOF
```

+ Step 2, create a timer for the service

```shell
cat << EOF > /etc/systemd/system/check-lvm-thinpool.timer
[Unit]
Description=Timer for Check LVM ThinPool

[Timer]
# Time between running each consecutive time
OnUnitActiveSec=1m

[Install]
WantedBy=timers.target
EOF
```

+ Reload daemon, enable and start the timer

```shell
systemctl daemon-reload
systemctl enable --now check-lvm-thinpool.timer
systemctl start check-lvm-thinpool.service
```

+ You can check timer status, and timers

```shell
systemctl status check-lvm-thinpool.timer
systemctl list-timers
```
