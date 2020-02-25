### Collectors enabled by default

#### Linux
Name     | Description 
---------|-------------
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
netstat | Exposes network statistics from `/proc/net/netstat`.
nfs | Exposes NFS client statistics from `/proc/net/rpc/nfs`.
nfsd | Exposes NFS kernel server statistics from `/proc/net/rpc/nfsd`.
sockstat | Exposes various statistics from `/proc/net/sockstat`.
stat | Exposes various statistics from `/proc/stat`.
textfile | Exposes statistics read from local disk.
time | Exposes the current system time.
timex | Exposes selected adjtimex(2) system call stats.
uname | Exposes system information as provided by the uname system call.
vmstat | Exposes statistics from `/proc/vmstat`.
systemd | Exposes service and system status from [systemd].

#### Windows
Name     | Description 
---------|-------------
cpu | CPU usage.
cs | system properties, num cpus/total memory.
container | Container metrics.
logical_disk | Logical disks, disk I/O.
logon | User logon sessions.
memory | Memory usage metrics.
net | Network interface I/O.
os | OS metrics (memory, processes, users).
service | Service state metrics.
system | System calls.
tcp | TCP connections.
textfile | Read prometheus metrics from a text file.

### Alert rules
Rule|node_Reboot
-|-
OS|Linux,Windows
Description|Reboots event detected recently.
Severity|critical
Expr|(rate(node_boot_time_seconds[10m]) != 0) or (rate(wmi_system_system_up_time[10m]) != 0)

Rule|node_PS_Load
-|- 
OS|Linux
Description|Processor load has exceeded the threshold.
Severity|warning
Expr|(node_load15 / count without (cpu, mode) (node_cpu_seconds_total{mode='system'})) > 5

Rule|node_CPU_Load
-|-
OS|Linux,Windows
Description|CPU usage has exceeded the threshold.
Severity|warning
Expr|((100 * (1 - avg by(subscription,region,environment,group,instance,service)(irate(node_cpu_seconds_total{mode="idle"}[10m])))) > 90) or ((100 * (1 - avg by(subscription,region,environment,group,instance,service)(irate(wmi_cpu_time_total{mode="idle"}[10m])))) > 90)