### Enabled by default



Name     | Description | OS
---------|-------------|----
arp | Exposes ARP statistics from `/proc/net/arp`.
bcache | Exposes bcache statistics from `/sys/fs/bcache/`.
conntrack | Shows conntrack statistics (does nothing if no `/proc/sys/net/netfilter/` present).
cpu | Exposes CPU statistics.
diskstats | Exposes disk I/O statistics.
entropy | Exposes available entropy.
filefd | Exposes file descriptor statistics from `/proc/sys/fs/file-nr`.
filesystem | Exposes filesystem statistics, such as disk space used.
hwmon | Expose hardware monitoring and sensor data from `/sys/class/hwmon/`.
infiniband | Exposes network statistics specific to InfiniBand and Intel OmniPath configurations.
ipvs | Exposes IPVS status from `/proc/net/ip_vs` and stats from `/proc/net/ip_vs_stats`.
loadavg | Exposes load average.
meminfo | Exposes memory statistics.
netclass | Exposes network interface info from `/sys/class/net/`
netdev | Exposes network interface statistics such as bytes transferred.
netstat | Exposes network statistics from `/proc/net/netstat`. This is the same information as `netstat -s`.
nfs | Exposes NFS client statistics from `/proc/net/rpc/nfs`. This is the same information as `nfsstat -c`.
nfsd | Exposes NFS kernel server statistics from `/proc/net/rpc/nfsd`. This is the same information as `nfsstat -s`.
sockstat | Exposes various statistics from `/proc/net/sockstat`.
stat | Exposes various statistics from `/proc/stat`. This includes boot time, forks and interrupts.
textfile | Exposes statistics read from local disk. The `--collector.textfile.directory` flag must be set.
thermal\_zone | Exposes thermal zone & cooling device statistics from `/sys/class/thermal`.
time | Exposes the current system time.
timex | Exposes selected adjtimex(2) system call stats.
uname | Exposes system information as provided by the uname system call.
vmstat | Exposes statistics from `/proc/vmstat`.
systemd | Exposes service and system status from [systemd].