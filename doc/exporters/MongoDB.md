#### Related Exporters or Integrations
- [mongodb_exporter](https://github.com/percona/mongodb_exporter)

#### Exporter collectors enabled by default
Name|Description 
-|-
collection|Collection of Collection metrics
database|Collection of Database metrics
indexusage|Collection of per index usage stats
topmetrics|Collection of table top metrics


#### Alert rules
Name|Severity|Description
-|-|-
MongoDB_Instance|critical|Instance has been stoped.
MongoDB_Rep_Lag|warning|Replication lag is more than 60 seconds.
MongoDB_Cursors_Num|warning|Too many cursors opened clients.
MongoDB_Cursors_Timeout|warning|Too many cursors are timing.
MongoDB_Connections|warning|Too many connections.
MongoDB_VirtualMemory_Usage|warning|High memory usage.


#### Reference third-party Ansible role for RHEL/CentOS
- [ansible-role-OS-consul](https://github.com/goldstrike77/ansible-role-OS-consul)
- [ansible-role-linux-prometheus](https://github.com/goldstrike77/ansible-role-linux-prometheus)
- [ansible-role-linux-mongodb](https://github.com/goldstrike77/ansible-role-linux-mongodb)
- [ansible-role-linux-grafana](https://github.com/goldstrike77/ansible-role-linux-grafana)

#### Recommendation Grafana Dashboards
- [MongoDB Overview](https://raw.githubusercontent.com/goldstrike77/Screenshots/master/Grafana/Databases/MongoDB_Overview.png)
- [MongoDB ReplSet](https://raw.githubusercontent.com/goldstrike77/Screenshots/master/Grafana/Databases/MongoDB_ReplSet.png)
- [MongoDB WiredTiger](https://raw.githubusercontent.com/goldstrike77/Screenshots/master/Grafana/Databases/MongoDB_WiredTiger.png)