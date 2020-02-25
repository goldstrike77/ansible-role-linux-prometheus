#### Related Exporters or Integrations
- [node_exporter](https://github.com/prometheus/node_exporter)
- [mysqld_exporter](https://github.com/percona/mysqld_exporter)

#### Exporter collectors enabled by default
Name     | Description 
---------|-------------
auto_increment.columns|Collect auto_increment columns and max values from information_schema.
binlog_size|Collect the current size of all registered binlog files.
engine_innodb_status|Collect from SHOW ENGINE INNODB STATUS.
global_status|Collect from SHOW GLOBAL STATUS.
global_variables|Collect from SHOW GLOBAL VARIABLES.
info_schema.clientstats|Collect client statistics.
info_schema.innodb_cmp|Collect InnoDB compressed tables metrics.
info_schema.innodb_cmpmem|Collect InnoDB buffer pool compression metrics.
info_schema.innodb_metrics|Collect metrics from information_schema.innodb_metrics.
info_schema.innodb_tablespaces|Collect metrics from information_schema.innodb_sys_tablespaces.
info_schema.processlist|Collect thread state counts from information_schema.processlist.
info_schema.query_response_time|Collect query response time distribution.
info_schema.schemastats|Collect schema statistics
info_schema.tables|Collect metrics from information_schema.tables.
info_schema.tablestats|Collect table statistics.
info_schema.userstats|Collect user statistics.
perf_schema.eventsstatements|Collect metrics from performance_schema.events_statements_summary_by_digest.
perf_schema.eventsstatementssum|Collect metrics from performance_schema.events_statements_summary_by_digest summed.
perf_schema.indexiowaits|Collect metrics from performance_schema.table_io_waits_summary_by_index_usage.
perf_schema.replication_group_member_stats|Collect metrics from performance_schema.replication_group_member_stats.
perf_schema.tableiowaits|Collect metrics from performance_schema.table_io_waits_summary_by_table.
perf_schema.tablelocks|Collect metrics from performance_schema.table_lock_waits_summary_by_table.
slave_hosts|Collect from SHOW SLAVE HOSTS.
slave_status|Collect from SHOW SLAVE STATUS.

#### Alert rules
Name|Severity|Description
-|-|-
MySQL_Instance|critical|Instance has been stoped.
MySQL_Rep|critical|Slave replication (IO or SQL) has been stoped.
MySQL_Rep_Lag|warning|The mysql slave replication has fallen behind and is not recovering.
MySQL_InnoDB_Log_Waits|warning|The innodb logs are waiting for disk.

#### Reference third-party Ansible role for RHEL/CentOS
- [ansible-role-OS-bootstrap](https://github.com/goldstrike77/ansible-role-OS-bootstrap)
- [ansible-role-OS-consul](https://github.com/goldstrike77/ansible-role-OS-consul)
- [ansible-role-linux-prometheus](https://github.com/goldstrike77/ansible-role-linux-prometheus)
- [ansible-role-linux-mysql](https://github.com/goldstrike77/ansible-role-linux-mysql)
- [ansible-role-linux-grafana](https://github.com/goldstrike77/ansible-role-linux-grafana)

#### Recommendation Grafana Dashboards
- MySQL InnoDB Metrics | [json](https://raw.githubusercontent.com/goldstrike77/ansible-role-linux-grafana/master/files/dashboards/Databases/MySQL_InnoDB_Metrics.json) | [png](https://raw.githubusercontent.com/goldstrike77/Screenshots/master/Grafana/Databases/MySQL_InnoDB_Metrics.png)
- MySQL MyISAM Aria_Metrics | [json](https://raw.githubusercontent.com/goldstrike77/ansible-role-linux-grafana/master/files/dashboards/Databases/MySQL_MyISAM_Aria_Metrics.json) | [png](https://raw.githubusercontent.com/goldstrike77/Screenshots/master/Grafana/Databases/MySQL_MyISAM_Aria_Metrics.png)
- MySQL Overview | [json](https://raw.githubusercontent.com/goldstrike77/ansible-role-linux-grafana/master/files/dashboards/Databases/MySQL_Overview.json) | [png](https://raw.githubusercontent.com/goldstrike77/Screenshots/master/Grafana/Databases/MySQL_Overview.png)
- MySQL Query Response Time | [json](https://raw.githubusercontent.com/goldstrike77/ansible-role-linux-grafana/master/files/dashboards/Databases/MySQL_Query_Response_Time.json) | [png](https://raw.githubusercontent.com/goldstrike77/Screenshots/master/Grafana/Databases/MySQL_Query_Response_Time.png)
- MySQL Replication | [json](https://raw.githubusercontent.com/goldstrike77/ansible-role-linux-grafana/master/files/dashboards/Databases/MySQL_Replication.json) | [png](https://raw.githubusercontent.com/goldstrike77/Screenshots/master/Grafana/Databases/MySQL_Replication.png)
- MySQL Table Statistics | [json](https://raw.githubusercontent.com/goldstrike77/ansible-role-linux-grafana/master/files/dashboards/Databases/MySQL_Table_Statistics.json) | [png](https://raw.githubusercontent.com/goldstrike77/Screenshots/master/Grafana/Databases/MySQL_Table_Statistics.png)
- MySQL User Statistics | [json](https://raw.githubusercontent.com/goldstrike77/ansible-role-linux-grafana/master/files/dashboards/Databases/MySQL_User_Statistics.json) | [png](https://raw.githubusercontent.com/goldstrike77/Screenshots/master/Grafana/Databases/MySQL_User_Statistics.png)