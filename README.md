![](https://img.shields.io/badge/Ansible-Prometheus-green.svg?logo=angular&style=for-the-badge)

>__Please note that the original design goal of this role was more concerned with the initial installation and bootstrapping environment, which currently does not involve performing continuous maintenance, and therefore are only suitable for testing and development purposes,  should not be used in production environments.__

>__请注意，此角色的最初设计目标更关注初始安装和引导环境，目前不涉及执行连续维护，因此仅适用于测试和开发目的，不应在生产环境中使用。__
___

<p><img src="https://raw.githubusercontent.com/goldstrike77/goldstrike77.github.io/master/img/logo/logo_prometheus.png" align="right" /></p>

__Table of Contents__

- [Architecture](#Architecture)
- [Components](#Components)
- [Overview](#overview)
- [Requirements](#requirements)
  * [Operating systems](#operating-systems)
  * [Prometheus versions](#Prometheus-versions)
  * [Distributed mode](#Distributed-mode)
- [ Role variables](#Role-variables)
  * [Main Configuration](#Main-parameters)
  * [Other Configuration](#Other-parameters)
- [Dependencies](#dependencies)
- [Example Playbook](#example-playbook)
  * [Hosts inventory file](#Hosts-inventory-file)
  * [Vars in role configuration](#vars-in-role-configuration)
  * [Combination of group vars and playbook](#combination-of-group-vars-and-playbook)
- [License](#license)
- [Author Information](#author-information)
- [Contributors](#Contributors)

## Architecture

                                     ┌───────────────┐
                                     │ Load Balancer │
                                     └───────┬───────┘
                                             ↓
                                     ┌───────┴───────┐
                                     │ Visualization ├┐
                                     └┬──────────────┘|
                                      └─ ─ ─ ┬ ─ ─ ─ ─┘
                                             ↓
                                     ┌───────┴───────┐
                                     │   Trickster   ├┐
                                     └┬──────────────┘|
                                      └─ ─ ─ ┬ ─ ─ ─ ─┘
                                             ↓
                                     ┌───────┴───────┐    ┌───────────────┐
                      ┌──────────┐   │     Query     ├┬─>─┤     Store     ├┐
                      │  Consul  │   └┬──────────────┘|   └┬──────────────┘|
                      └───────┬──┘    └─ ─ ─ ┬ ─ ─ ─ ─┘    └─ ─ ─ ┬ ─ ─ ─ ─┘
                              ↑              ↓                    ↓
        ┌──────────────┐    ┌─┴──────────┬───┴─────┐    ┌─────────┴─────┐
        │ Alertmanager ├┬─<─┤ Prometheus < Sidecar ├┬─>─┤       S3      │
        └┬─────────────┘|   └┬───────────┴┬────────┘|   └───────┬───────┘
         └─ ─ ─ ─ ─ ─ ─ ┘    └─ ─ ─ ─ ─ ─ ┴ ─ ─ ─ ─ ┘           ↑
                                                        ┌───────┴───────┐
                                                        │    Compact    │
                                                        └───────────────┘
                                                        
## Components
### Trickster
Trickster's proxy inspects the time range of a client query to determine what data points are already cached, and requests from Prometheus only the data points still needed to service the client request. This results in dramatically faster chart load times for everyone.
### Thanos Query
The Query component is stateless and horizontally scalable and can be deployed with any number of replicas. Once connected to the Sidecars, it automatically detects which Prometheus servers need to be contacted for a given PromQL query. Also capable of deduplicating data collected from Prometheus distributed.
### Thanos Sidecar
Sidecar is to backup Prometheus data into an Object Storage bucket, and giving other Thanos components access to the Prometheus instance the Sidecar is attached to.
### Thanos Store (optional)
As the sidecar backs up data into the object storage of your choice, you can decrease Prometheus retention and store less locally. It can find in your object storage bucket.
### Thanos Compact (optional)
The compact component simple scans the object storage and processes compaction where required. At the same time it is responsible for creating downsampled copies of data to speed up queries.
### Prometheus
Monitoring system and time series database.
### Alertmanager
Handles alerts sent by Prometheus server.
### Consul
Targets service discovery.
### S3 (optional)
Object storage service.

## Overview
This Ansible role installs standalone or distributed prometheus v2 server on linux operating system, including establishing a filesystem structure and server configuration with some common operational features.

>__Performance is severely affected by remote reading and deduplicating data queries, thanos store components + object storage offers only if need long term storage.__

## Requirements
### Operating systems
This role will work on the following operating systems:

  * CentOS 7

### Prometheus versions

The following list of supported the Prometheus releases:

* Prometheus v2.2.1+ 

### Distributed mode
* Targets will be dispersed if greater than 3 prometheus nodes.
* Use the last digital of the IP Address or host number to spread the load between multiple prometheus nodes.

for example:

        node01: - source_labels: [__address__]
                  regex: .+[02468]:.+
                  action: drop
        node02: - source_labels: [__address__]
                  regex: .+[13579]:.+
                  action: drop
        node03: - source_labels: [__address__]
                  regex: .+[02468]:.+
                  action: drop
        node04: - source_labels: [__address__]
                  regex: .+[13579]:.+
                  action: drop

## Role variables
### Main parameters #
There are some variables in defaults/main.yml which can (Or needs to) be overridden:

##### General parameters
* `thanos_is_install`: A boolean value, whether install the Thanos.
* `trickster_is_install`: A boolean value, whether install the Trickster.
* `thanos_bucket_is_used`: A boolean value, whether use object storage.
* `prometheus_conf_path`: Specify the Prometheus data directory.
* `prometheus_data_path`:  Specify the Prometheus configure directory.
* `prometheus_consul_server`: The consul address and port.
* `prometheus_consul_token`: The consul acl token.
* `thanos_cluster_secret_key`: Initial secret key to encrypt cluster gossip.

##### Service Mesh
* `environments`: Define the service environment.
* `consul_public_register`: Whether register a exporter service with public consul client.
* `consul_public_exporter_token`: Public Consul client ACL token.
* `consul_public_clients`: List of public consul clients.
* `consul_public_http_port`: The consul HTTP API port.

##### Listen port
* `prometheus_port.alertmanager`: alertmanager instance listen port.
* `prometheus_port.cluster`:  alertmanager cluster listen port.
* `prometheus_port.prometheus`: Prometheus instance listen port.
* `thanos_port.compact_http`: Port for compact HTTP endpoints.
* `thanos_port.query_cluster`: Port for query gossip cluster.
* `thanos_port.query_grpc`: Port for query gRPC endpoints.
* `thanos_port.query_http`: Port for query HTTP endpoints.
* `thanos_port.sidecar_cluster`: Port for sidecar gossip cluster.
* `thanos_port.sidecar_grpc`: Port for sidecar gRPC endpoints.
* `thanos_port.sidecar_http`: Port for sidecar  HTTP endpoints.
* `thanos_port.store_cluster`: Port for store gossip cluster.
* `thanos_port.store_grpc`: Port for store gRPC endpoints.
* `thanos_port.store_http`: Port for store HTTP endpoints.
* `trickster_port.proxy_server`: Defines the port on which Trickster's Proxy server listens.
* `trickster_port.metrics`: Defines the port that Trickster's metrics server listens on at /metrics.

##### Grafana parameters
* `prometheus_grafana_dept`: A boolean value, whether install the Grafana for metrics visualization.
* `prometheus_grafana_path`: Specify the Grafana data directory.
* `prometheus_grafana_version`: Specify the Grafana version.
* `prometheus_grafana_admin_user`: The name of the default Grafana admin user.
* `prometheus_grafana_admin_password`: The password of the default Grafana admin.
* `prometheus_grafana_port`: Grafana instance listen port.
* `prometheus_grafana_redis_dept`: A boolean value, whether installs Redis.
* `prometheus_grafana_redis_path`: Specify the Redis data directory.
* `prometheus_grafana_redis_requirepass`: Authorization clients password.
* `prometheus_grafana_redis_maxmemory`: A memory usage limit to the specified amount in MB.
* `prometheus_grafana_redis_hosts`: Redis hosts address.
* `prometheus_grafana_redis_port`: Redis listen port.
* `prometheus_grafana_ngx_dept`: A boolean value, whether proxy Grafana web interface using.
* `prometheus_grafana_ngx_block_agents`: Enables or disables block unsafe User Agents.
* `prometheus_grafana_ngx_block_string`: Enables or disables block includes Exploits / File injections / Spam / SQL injections.
* `prometheus_grafana_ngx_compress`: Enables or disables compression.
* `prometheus_grafana_ngx_domain`: Defines domain name.
* `prometheus_grafana_ngx_pagespeed`: Enables or disables pagespeed modules.
* `prometheus_grafana_ngx_port_http`: NGinx HTTP listen port.
* `prometheus_grafana_ngx_port_https`: NGinx HTTPs listen port.
* `prometheus_grafana_ngx_ssl_protocols`: intermediate or modern, defines SSL protocol profile.
* `prometheus_grafana_ngx_version`: extras or standard

##### Prometheus System Variables
* `prometheus_arg.evaluation_interval`: How frequently to evaluate rules.
* `prometheus_arg.query_max_concurrency`: Maximum number of queries executed concurrently.
* `prometheus_arg.refresh_interval`: Refresh interval to re-read the instance list.
* `prometheus_arg.sd_conf`: Consul service discovery configurations.
* `prometheus_arg.scrape_interval`: How frequently to scrape targets from this job.
* `prometheus_arg.scrape_timeout`: How long until a scrape request times out.
* `prometheus_arg.storage_tsdb_block_duration`: Must be set to equal values to disable local compaction.
* `prometheus_arg.storage_tsdb_retention`: How long to retain samples in storage.
* `prometheus_arg.ulimit_core`: The number of coredump launched by systemd.
* `prometheus_arg.ulimit_memlock`: The number of memory lock launched by systemd.
* `prometheus_arg.ulimit_nofile`: The number of files launched by systemd.
* `prometheus_arg.ulimit_nproc`: The number of processes launched by systemd.

##### Alertmanager System Variables
* `alertmanager_arg.mail_to_user`: The email address to send notifications to.
* `alertmanager_arg.group_by`: They are grouped by alertname.
* `alertmanager_arg.group_wait`: How long to initially wait to send a notification for a group of alerts.
* `alertmanager_arg.group_interval`: How long to wait before sending a notification about new alerts that are added to a group.
* `alertmanager_arg.repeat_interval`: How long to wait before sending a notification again if it has already been sent successfully for an alert.
* `alertmanager_arg.smtp_auth_password`: SMTP authentication password.
* `alertmanager_arg.smtp_auth_username`: SMTP authentication username.
* `alertmanager_arg.smtp_from`: The sender address.
* `alertmanager_arg.smtp_require_tls`: The SMTP TLS requirement.
* `alertmanager_arg.smtp_smarthost`: The SMTP host through which emails are sent.
* `alertmanager_arg.wechat_api_agentid`: The wechat agent ID.
* `alertmanager_arg.wechat_api_url`: The WeChat API URL.
* `alertmanager_arg.wechat_to_user`: The WeChat user to send notifications to.
* `alertmanager_arg.wechat_api_corpid`:  The corp id for authentication.
* `alertmanager_arg.wechat_api_secret`: The API key to use when talking to the WeChat API.

##### Alertmanager rules
* `prometheus_alert_rules`: Defining alerting rules.

##### Thanos System Variables
* `thanos_arg.chunk_pool_size`: Maximum size of concurrently allocatable bytes for chunks.
* `thanos_arg.cluster_gossip_interval`: Between sending gossip messages.
* `thanos_arg.cluster_pushpull_interval`: Between gossip state syncs
* `thanos_arg.cluster_refresh_interval`: Membership to refresh.
* `thanos_arg.index_cache_size`: Maximum size of items held in the index cache.
* `thanos_arg.log_format`: Log format.
* `thanos_arg.log_level`: Log level.
* `thanos_arg.query_auto_downsampling`: Enable automatic adjustment source data.
* `thanos_arg.query_max_concurrent`: Maximum number of queries processed concurrently.
* `thanos_arg.query_timeout`: Maximum time to process query.
* `thanos_arg.ulimit_core`: The number of coredump launched by systemd.
* `thanos_arg.ulimit_memlock`: The number of memory lock launched by systemd.
* `thanos_arg.ulimit_nofile`: The number of files launched by systemd.
* `thanos_arg.ulimit_nproc`: The number of processes launched by systemd.
* `thanos_arg.version`: Specify the Thanos version.

##### Thanos objstore Variables
* `thanos_obj_arg.type`: Objstore service type.
* `thanos_obj_arg.bucket`: Object storage bucket name.
* `thanos_obj_arg.endpoint`: Objstore service endpoint.
* `thanos_obj_arg.insecure`: Objstore service 
* `thanos_obj_arg.signature_version2`: I don't know what that means.
* `thanos_obj_arg.access_key`:  AccessKeyID.
* `thanos_obj_arg.secret_key`: SecretAccessKey.

##### Trickster System Variables
* `trickster_arg.cache_type`: Defines what kind of cache Trickster uses.
* `trickster_arg.compression`: Determines whether the cache should be compressed.
* `trickster_arg.default_step`: I don't know what it means.
* `trickster_arg.fast_forward_disable`: Turn off the 'fast forward' feature for any requests proxied.
* `trickster_arg.ignore_no_cache_header`: Disables a client's ability to send a no-cache to refresh a cached query.
* `trickster_arg.max_value_age_secs`: Defines the maximum age of specific datapoints in seconds.
* `trickster_arg.reap_sleep_ms`: Defines how long the cache reaper waits between reap cycles.
* `trickster_arg.record_ttl_secs`: Defines the relative expiration of cached queries.
* `trickster_arg.ulimit_core`: The number of coredump launched by systemd.
* `trickster_arg.ulimit_memlock`:  The number of memory lock launched by systemd.
* `trickster_arg.ulimit_nofile`: The number of files launched by systemd.
* `trickster_arg.ulimit_nproc`: The number of processes launched by systemd.
* `trickster_arg.version`: Specify the Trickster version.

### Other parameters
There are some variables in vars/main.yml:

## Dependencies
- Ansible versions > 2.6 are supported.
- [Grafana](https://github.com/goldstrike77/ansible-role-linux-grafana.git)
- [NGinx](https://github.com/goldstrike77/ansible-role-linux-nginx.git)
- [Redis](https://github.com/goldstrike77/ansible-role-linux-redis.git)

## Example

### Hosts inventory file
See tests/inventory for an example, all host must belong to one child group.

    [demo_mon]
    node01 ansible_host='192.168.1.10' prometheus_is_install='true'
    node02 ansible_host='192.168.1.11' prometheus_is_install='true'
    node03 ansible_host='192.168.1.12' prometheus_is_install='true'

### Vars in role configuration
Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: all
      roles:
         - role: ansible-role-linux prometheus
           prometheus_is_install: true

### Combination of group vars and playbook
You can also use the group_vars or the host_vars files for setting the variables needed for this role. File you should change: group_vars/all or host_vars/`group_name`

    prometheus_conf_path: '/etc/prometheus'
    prometheus_data_path: '/data'
    thanos_is_install: true
    trickster_is_install: true
    thanos_bucket_is_used: true
    prometheus_consul_server: '127.0.0.1:8500'
    prometheus_consul_token: '7471828c-d50a-4b25-b6a5-d80f02a03bae'
    thanos_cluster_secret_key: '7A25432A462D4A614E645267556B5870'
    prometheus_port: 
      alertmanager: '9093'
      cluster: '9094'
      prometheus: '9090'
    thanos_port:
      compact_http: '19194'
      query_cluster: '19292'
      query_grpc: '19092'
      query_http: '19192'
      sidecar_cluster: '19291'
      sidecar_grpc: '19091'
      sidecar_http: '19191'
      store_cluster: '19293'
      store_grpc: '19093'
      store_http: '19193'
    trickster_port:
      proxy_server: '19090'
      metrics: '8082'
    prometheus_grafana_dept: false
    prometheus_grafana_path: '/data'
    prometheus_grafana_version: '5'
    prometheus_grafana_admin_user: 'admin'
    prometheus_grafana_admin_password: 'password'
    prometheus_grafana_port: '3000'
    prometheus_grafana_redis_dept: false
    prometheus_grafana_redis_path: '{{ prometheus_data_path }}'
    prometheus_grafana_redis_requirepass: 'password'
    prometheus_grafana_redis_maxmemory: '1'
    prometheus_grafana_redis_hosts: '{{ prometheus_server[0] }}'
    prometheus_grafana_redis_port: '6379'
    prometheus_grafana_ngx_dept: false
    prometheus_grafana_ngx_block_agents: false
    prometheus_grafana_ngx_block_string: false
    prometheus_grafana_ngx_compress: false
    prometheus_grafana_ngx_domain: 'visual.example.com'
    prometheus_grafana_ngx_pagespeed: false
    prometheus_grafana_ngx_port_http: '80'
    prometheus_grafana_ngx_port_https: '443'
    prometheus_grafana_ngx_ssl_protocols: 'modern'
    prometheus_grafana_ngx_version: 'extras'
    prometheus_arg:
      evaluation_interval: '30s'
      query_max_concurrency: '256'
      refresh_interval: '60s'
      sd_conf: 'consul_sd_configs'
      scrape_interval: '60s'
      scrape_timeout: '10s'
      storage_tsdb_block_duration: '2h'
      storage_tsdb_retention: '30d'
      ulimit_core: 'infinity'
      ulimit_memlock: 'infinity'
      ulimit_nofile: '262144'
      ulimit_nproc: '262144'
    alertmanager_arg:
      mail_to_user: 'somebody@somebody.com'
      group_by:
        - 'alertname'
        - 'cluster'
        - 'service'
      group_wait: '60s'
      group_interval: '60s'
      repeat_interval: '2h'
      smtp_auth_password: 'password'
      smtp_auth_username: 'user'
      smtp_from: 'do-not-reply@somebody.com'
      smtp_require_tls: 'false'
      smtp_smarthost: '127.0.0.1:25'
      wechat_api_agentid: '0'
      wechat_api_url: 'https://qyapi.weixin.qq.com/cgi-bin/'
      wechat_to_user: 'xxxxxxx'
      wechat_api_corpid: 'wxe787605fxxxxxxxx'
      wechat_api_secret: 'fm6Ehm6DI8PlGWxtKcgkDOZCLMTsNqKqTxxxxxxxxxx'
    prometheus_alert_rules:
      - 'Consul'
      - 'ElasticSearch'
    thanos_arg:
      chunk_pool_size: '2GB'
      cluster_gossip_interval: '10s'
      cluster_pushpull_interval: '10s'
      cluster_refresh_interval: '1m'
      index_cache_size: '1GB'
      log_format: 'logfmt'
      log_level: 'info'
      query_auto_downsampling: false
      query_max_concurrent: '200'
      query_timeout: '1m'
      ulimit_core: 'infinity'
      ulimit_memlock: 'infinity'
      ulimit_nofile: '262144'
      ulimit_nproc: '262144'
      version: '0.3.2'
    thanos_obj_arg:
      type: 'S3'
      bucket: 'thanos'
      endpoint: '127.0.0.1:9001'
      insecure: 'true'
      signature_version2: 'false'
      access_key: 'QTNTQZZP1NOBNCL5LPRX'
      secret_key: 'b1mPOhMQc8JP49Jy8pJLsDwHayDtBFC3M9YxSmzM'
    trickster_arg:
      cache_type: 'memory'
      compression: 'true'
      default_step: '300'
      fast_forward_disable: 'false'
      ignore_no_cache_header:
      max_value_age_secs:
      reap_sleep_ms: '1000'
      record_ttl_secs: '43200'
      ulimit_core: 'infinity'
      ulimit_memlock: 'infinity'
      ulimit_nofile: '262144'
      ulimit_nproc: '262144'
      version: '0.1.7'
    environments: 'SIT'
    consul_public_register: false
    consul_public_exporter_token: '00000000-0000-0000-0000-000000000000'
    consul_public_clients: 'localhost'
    consul_public_http_port: '8500'

## License

![](https://img.shields.io/badge/MIT-purple.svg?style=for-the-badge)

## Author Information
Please send your suggestions to make this role better.

## Contributors
Special thanks to the [Connext Information Technology](http://www.connext.com.cn) for their contributions to this role.
