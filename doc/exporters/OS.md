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
Name|node_Reboot
| :---| :---
OS|Linux,Windows
Description|Reboots event detected recently.
Severity|critical
Expr|(rate(node_boot_time_seconds[10m]) != 0) or (rate(wmi_system_system_up_time[10m]) != 0)

Name|node_PS_Load
| :---| :---
OS|Linux
Description|Processor load has exceeded the threshold.
Severity|warning
Expr|(node_load15 / count without (cpu, mode) (node_cpu_seconds_total{mode='system'})) > 5

Name|node_CPU_Load
| :---| :---
OS|Linux,Windows
Description|CPU usage has exceeded the threshold.
Severity|warning
Expr|((100 * (1 - avg by(subscription,region,environment,group,instance,service)(irate(node_cpu_seconds_total{mode="idle"}[10m])))) > 90) or ((100 * (1 - avg by(subscription,region,environment,group,instance,service)(irate(wmi_cpu_time_total{mode="idle"}[10m])))) > 90)

Rule|node_RAM_Usage
| :---| :---
OS|Linux,Windows
Description|Memory usage has exceeded the threshold.
Severity|warning
Expr|((sum(node_memory_MemTotal_bytes)by(environment,group,instance,service) - sum(node_memory_MemFree_bytes + node_memory_Buffers_bytes + node_memory_Cached_bytes )by(environment,group,instance,service)) / (sum(node_memory_MemTotal_bytes)by(environment,group,instance,service)) * 100 > 90) or ((sum(wmi_cs_physical_memory_bytes)by(environment,group,instance,service) - sum(wmi_os_physical_memory_free_bytes)by(environment,group,instance,service)) / (sum(wmi_cs_physical_memory_bytes)by(environment,group,instance,service)) * 100 > 90)

Rule|node_FS_Usage
| :---| :---
OS|Linux,Windows
Description|Filesystem has less than 10%.
Severity|critical
Expr|(node_filesystem_free_bytes{fstype!~"rootfs|selinuxfs|autofs|rpc_pipefs|tmpfs"} / node_filesystem_size_bytes{fstype!~"rootfs|selinuxfs|autofs|rpc_pipefs|tmpfs"} < 0.1) or (wmi_logical_disk_free_bytes{volume!~"HarddiskVolume.*"} / wmi_logical_disk_size_bytes{volume!~"HarddiskVolume.*"} < 0.1)

Rule|node_FS_Usage
| :---| :---
OS|Linux,Windows
Description|Filesystem has less than 20%.
Severity|warning
Expr|(node_filesystem_free_bytes{fstype!~"rootfs|selinuxfs|autofs|rpc_pipefs|tmpfs"} / node_filesystem_size_bytes{fstype!~"rootfs|selinuxfs|autofs|rpc_pipefs|tmpfs"} < 0.2) or (wmi_logical_disk_free_bytes{volume!~"HarddiskVolume.*"} / wmi_logical_disk_size_bytes{volume!~"HarddiskVolume.*"} < 0.2)

Rule|node_FS_Inodes
| :---| :---
OS|Linux
Description|Filesystem inodes has less than 10%.
Severity|warning
Expr|node_filesystem_files_free{fstype!~"rootfs\|selinuxfs\|autofs\|rpc_pipefs|tmpfs"} / node_filesystem_files{fstype!~"rootfs|selinuxfs|autofs|rpc_pipefs|tmpfs"} * 100 < 10

Rule|node_Disc_Read
| :---| :---
OS|Linux
Description|Disk read latency > 100ms.
Severity|warning
Expr|irate(node_disk_read_time_seconds_total{device!~"dm-.+"}[5m]) / irate(node_disk_reads_completed_total{device!~"dm-.+"}[5m]) > 0.1

Rule|node_Disc_Write
| :---| :---
OS|Linux
Description|Disk write latency > 100ms.
Severity|warning
Expr|irate(node_disk_write_time_seconds_total{device!~"dm-.+"}[5m]) / irate(node_disk_writes_completed_total{device!~"dm-.+"}[5m]) > 0.1

Rule|node_Disc_Read
| :---| :---
OS|Linux,Windows
Description|Disk is probably reading too much data.
Severity|warning
Expr|(sum by (subscription,region,environment,group,instance,service) (irate(node_disk_read_bytes_total{device!~"dm-.+"}[5m])) / 1024 / 1024 > 50) or (sum by (subscription,region,environment,group,instance,service) (irate(wmi_logical_disk_read_bytes_total{volume!~"HarddiskVolume.*"}[5m])) / 1024 / 1024 > 50)

Rule|node_Disc_Write
| :---| :---
OS|Linux,Windows
Description|Disk is probably writing too much data.
Severity|warning
Expr|(sum by (subscription,region,environment,group,instance,service) (irate(node_disk_written_bytes_total{device!~"dm-.+"}[5m])) / 1024 / 1024 > 50) or (sum by (subscription,region,environment,group,instance,service) (irate(wmi_logical_disk_write_bytes_total{volume!~"HarddiskVolume.*"}[5m])) / 1024 / 1024 > 50)

Rule|node_NIC_Receive
| :---| :---
OS|Linux,Windows
Description|Has more than 300M receive.
Severity|warning
Expr|(irate(node_network_receive_bytes_total{device!~"lo|docker.*"}[5m]) > 300000000) or (irate(wmi_net_bytes_received_total[5m]) > 300000000)

Rule|node_NIC_Transmit
| :---| :---
OS|Linux,Windows
Description|Has more than 300M traffic.
Severity|warning
Expr|(irate(node_network_transmit_bytes_total{device!~"lo|docker.*"}[5m]) > 30000000) or (irate(wmi_net_bytes_sent_total[5m]) > 30000000)

Rule|node_Service
| :---| :---
OS|Linux,Windows
Description|Service has been stoped.
Severity|warning
Expr|(node_systemd_unit_state{state="inactive"} == 1) or (wmi_service_state{state="stopped"} == 1)

### Reference Ansible role
- [ansible-role-OS-bootstrap](https://github.com/goldstrike77/ansible-role-OS-bootstrap)
- [ansible-role-OS-consul](https://github.com/goldstrike77/ansible-role-OS-consul)
- [ansible-role-linux-prometheus](https://github.com/goldstrike77/ansible-role-linux-prometheus)
- [ansible-role-linux-grafana](https://github.com/goldstrike77/ansible-role-linux-grafana)

### Grafana dashboards
- [Cross Server Graphs](https://raw.githubusercontent.com/goldstrike77/Screenshots/master/Grafana/Universal/Cross_Server_Graphs.png)
- [Linux Disk Performance](https://raw.githubusercontent.com/goldstrike77/Screenshots/master/Grafana/OperatingSystem/Linux_Disk_Performance.png)
- [Linux Disk Space](https://raw.githubusercontent.com/goldstrike77/Screenshots/master/Grafana/OperatingSystem/Linux_Disk_Space.png)
- [Linux Network Overview](https://raw.githubusercontent.com/goldstrike77/Screenshots/master/Grafana/OperatingSystem/Linux_Network_Overview.png)
- [Linux System Overview](https://raw.githubusercontent.com/goldstrike77/Screenshots/master/Grafana/OperatingSystem/Linux_System_Overview.png)
- [Windows System Overview](https://raw.githubusercontent.com/goldstrike77/Screenshots/master/Grafana/OperatingSystem/Windows_System_Overview.png)