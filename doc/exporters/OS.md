### Hardware and OS metrics exposed

#### Related Exporters or Integrations
- [node_exporter](https://github.com/prometheus/node_exporter)
- [wmi_exporter](https://github.com/martinlindhe/wmi_exporter)

#### Exporter collectors enabled by default

##### Linux
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

##### Windows
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

#### Alert rules
Name|OS|Severity|Description
-|-|-|-
node_Reboot|Linux,Windows|critical|Reboots event detected recently.
node_PS_Load|Linux|warning|Processor load has exceeded the threshold.
node_CPU_Load|Linux,Windows|warning|CPU usage has exceeded the threshold.
node_RAM_Usage|Linux,Windows|warning|Memory usage has exceeded the threshold.
node_FS_Usage|Linux,Windows|critical|Filesystem has less than 10%.
node_FS_Usage|Linux,Windows|warning|Filesystem has less than 20%.
node_FS_Inodes|Linux|warning|Filesystem inodes has less than 10%.
node_Disc_Read|Linux|warning|Disk read latency > 100ms.
node_Disc_Write|Linux|warning|Disk write latency > 100ms.
node_Disc_Read|Linux,Windows|warning|Disk is probably reading too much data.
node_Disc_Write|Linux,Windows|warning|Disk is probably writing too much data.
node_NIC_Receive|Linux,Windows|warning|Has more than 300M receive.
node_NIC_Transmit|Linux,Windows|warning|Has more than 300M traffic.
node_Service|Linux,Windows|warning|Service has been stoped.

#### Reference third-party Ansible role for RHEL/CentOS
- [ansible-role-OS-bootstrap](https://github.com/goldstrike77/ansible-role-OS-bootstrap)
- [ansible-role-OS-consul](https://github.com/goldstrike77/ansible-role-OS-consul)
- [ansible-role-linux-prometheus](https://github.com/goldstrike77/ansible-role-linux-prometheus)
- [ansible-role-linux-grafana](https://github.com/goldstrike77/ansible-role-linux-grafana)

#### Recommendation Grafana Dashboards
- Cross Server Graphs | [json](https://raw.githubusercontent.com/goldstrike77/ansible-role-linux-grafana/master/files/dashboards/Universal/Cross_Server_Graphs.json) | [png](https://raw.githubusercontent.com/goldstrike77/Screenshots/master/Grafana/Universal/Cross_Server_Graphs.png)
- Linux Disk Performance | [json](https://raw.githubusercontent.com/goldstrike77/ansible-role-linux-grafana/master/files/dashboards/OperatingSystem/Linux_Disk_Performance.json) | [png](https://raw.githubusercontent.com/goldstrike77/Screenshots/master/Grafana/OperatingSystem/Linux_Disk_Performance.png)
- Linux Disk Space | [json](https://raw.githubusercontent.com/goldstrike77/ansible-role-linux-grafana/master/files/dashboards/OperatingSystem/Linux_Disk_Space.json) | [png](https://raw.githubusercontent.com/goldstrike77/Screenshots/master/Grafana/OperatingSystem/Linux_Disk_Space.png)
- Linux Network Overview | [json](https://raw.githubusercontent.com/goldstrike77/ansible-role-linux-grafana/master/files/dashboards/OperatingSystem/Linux_Network_Overview.json) | [png](https://raw.githubusercontent.com/goldstrike77/Screenshots/master/Grafana/OperatingSystem/Linux_Network_Overview.png)
- Linux System Overview | [json](https://raw.githubusercontent.com/goldstrike77/ansible-role-linux-grafana/master/files/dashboards/OperatingSystem/Linux_System_Overview.json) | [png](https://raw.githubusercontent.com/goldstrike77/Screenshots/master/Grafana/OperatingSystem/Linux_System_Overview.png)
- Windows System Overview | [json](https://raw.githubusercontent.com/goldstrike77/ansible-role-linux-grafana/master/files/dashboards/OperatingSystem/Windows_System_Overview.json) | [png](https://raw.githubusercontent.com/goldstrike77/Screenshots/master/Grafana/OperatingSystem/Windows_System_Overview.png)