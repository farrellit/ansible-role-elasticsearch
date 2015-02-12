elasticsearch
=============

This role will install/un-install and configure Elasticsearch, managing the process with supervisord.  Many settings from the elasticsearch.yml config file have been specifically called out here as they are the most commonly used and may include defaults.  Any additional settings not called out here can be included as a hash to the elasticsearch_conf_additional variable. As Ansible does not allow hash merging, this method is required.
Requirements
------------

Java is required to be installed outside of this role.  Supervisor will be installed via a package by this role.

Role Variables
--------------

When including true or false for elasticsearch config variables, enclose them in single quotes to ensure they are passed lowercase

| variable | description | default | mandatory
|----------|-------------|---------|----------
| `elasticsearch_state` | present or absent | present |
| `elasticsearch_install_method` | s3 or package | s3 |
| `elasticsearch_install_path` | path to extract elasticsearch if using s3 install method | /opt |
| `elasticsearch_version` | version to install | 1.4.2 |
| `elasticsearch_remove_old` | remove old versions of elasticsearch before installing | true |
| `elasticsearch_s3_bucket` | s3 bucket to find installation artifact | none | yes (if using s3 install method)
| `elasticsearch_s3_base` | base prefix in the s3 bucket to find installation artifact | none | yes (if using s3 install method)
| `elasticsearch_user` | user to install/run elasticsearch as | elasticsearch |
| `elasticsearch_path_logs` | path to place logs | /var/log/elasticsearch |
| `elasticsearch_path_conf` | path to place config (elasticsearch.yml & logging.yml) | /etc/elasticsearch |
| `elasticsearch_path_work` | path to place work files | "{{ elasticsearch_install_path }}/elasticsearch/work" |
| `elasticsearch_path_data` | path to place data files | "{{ elasticsearch_install_path }}/elasticsearch/data" |
| `elasticsearch_path_plugins` | path to place plugin files | "{{ elasticsearch_install_path }}/elasticsearch/plugins" |
| `elasticsearch_java_home` | java home to use when starting elasticsearch | none | no
| `elasticsearch_java_opts` | additional java options you | none | no
| `elasticsearch_autostart` | start elasticsearch on system start | 'true' |
| `elasticsearch_supervisor_state` | state to leave elasticsearch in after install, started or stopped | started |
| `elasticsearch_plugins` | list of hashes containing names of plugins to install. ex - name: magical_plugin | none | no
| `elasticsearch_max_open_files` | set the limit for max open files for the elasticsearch_user | 64000 |
| `elasticsearch_max_locked_memory` | set the limit for max locked memory for the elasticsearch user | unlimited |
| `elasticsearch_heap_size` | size of the heap for elasticsearch | none* | no
| `elasticsearch_heap_newsize` | size of the new generation heap | none* | no
| `elasticsearch_direct_size` | max size of the direct memory | none* | no
| `elasticsearch_cluster_name` | name of the cluster | none* | no
| `elasticsearch_node_name` | name of the current node | "{{ inventory_hostname }}-es" |
| `elasticsearch_cluster_routing_allocation_awareness_attributes` | attributes to use when determining where to allocate shards | none | no
| `elasticsearch_node_master` | is this node a master | 'true' |
| `elasticsearch_node_data` | does this node hold data | 'true' |
| `elasticsearch_node_max_local_storage_nodes` | maximum data nodes on the local instance | none* | no
| `elasticsearch_node_extras` | list of hashes containing node.attribute: value parameters. especially useful for allocation awareness | none | no
| `elasticsearch_index_number_of_shards` | number of shards per index | 5 |
| `elasticsearch_index_number_of_replicas` | number of replicas per shard | 1 |
| `elasticsearch_index_translog_flush_threshold_ops` | number of operations buffered before flushing translog | none* | no
| `elasticsearch_index_memory_index_buffer_size` | amount of memory allocated for indexing (percentage or mb) | none* | no
| `elasticsearch_plugin_mandatory` | list of plugins mandatory for node startup | none | no
| `elasticsearch_bootstrap_mlockall` | lock all heap on startup | 'true' |
| `elasticsearch_network_bind_host` | control the host different network components will bind on | none* | no
| `elasticsearch_network_publish_host` | control the host the node will publish itself within the cluster | none* | no
| `elasticsearch_network_host` | sets network.bind_host and network.publish_host to the same value | 0.0.0.0 |
| `elasticsearch_transport_tcp_port` | port range used for internal transport | '9300-9400' |
| `elasticsearch_http_port` | http port to expose for api and bulk transactions | 9200 |
| `elasticsearch_tcp_compress` | enable LZF compression between nodes | 'false' |
| `elasticsearch_http_max_content_length` | max content of an HTTP request | none* | no
| `elasticsearch_http_enabled` | enable http (api etc) | 'true'
| `elasticsearch_gateway_type` | elasticsearch cluster state storage | local |
| `elasticsearch_gateway_recover_after_nodes` | when x nodes are in the cluster, begin recovery after gateway.recovery_after_time | none | no
| `elasticsearch_gateway_recover_after_data_nodes` | when x data nodes are in the cluster, begin recovery after gateway.recovery_after_time | none | no
| `elasticsearch_gateway_recover_after_master_nodes` | when x master nodes are in the cluster, begin recovery after gateway.recovery_after_time | none | no
| `elasticsearch_gateway_recover_after_time` | when x has elapsed and all recovery_after_nodes are met, begin recovery | none* | no
| `elasticsearch_gateway_expected_nodes` | when x nodes are in the cluster, begin recovery immediately | none | no
| `elasticsearch_gateway_expected_data_nodes` | when x data nodes are in the cluster, begin recovery immediately | none | no
| `elasticsearch_gateway_expected_master_nodes` | when x master nodes are in the cluster, begin recovery immediately | none | no
| `elasticsearch_cluster_routing_allocation_node_concurrent_recoveries` | concurrent recovery operations per node | none* | no
| `elasticsearch_indices_recovery_max_bytes_per_sec` | max bytes per second used for recovery | none | no
| `elasticsearch_indices_recovery_concurrent_stream` | number of streams to open to peer nodes when recovering shard | none* | no
| `elasticsearch_discovery_zen_minimum_master_nodes` | min number of master eligible nodes a node should "see" in order to win a master election | none | no
| `elasticsearch_discovery_zen_ping_timeout` | timeout before ping failure | 3s |
| `elasticsearch_discovery_zen_ping_multicast_enabled` | enabled multicast discovery (not supported in aws) | 'true' |
| `elasticsearch_discovery_zen_ping_unicast_hosts` | an array setting or a comma delimited value in the form of host:port, or in the form of host[port1-port2] | none | no
| `elasticsearch_script_disable_dynamic` | enable dynamic scripting | 'false' |
| `elasticsearch_http_cors_enabled` | enable or disable cross-origin resource sharing | 'false' |
| `elasticsearch_http_cors_allow_origin` | origins to allow. prepend and append a / to the value, value will be treated as a regular expression | none* | no
| `elasticsearch_plugin_aws` | install aws plugin | 'false' |
| `elasticsearch_plugin_aws_ec2_groups` | comma separated list of security groups needed for discovery
| `elasticsearch_plugin_aws_availability_zones` | comma separated list of availability zones for discovery | none | no
| `elasticsearch_plugin_aws_any_group` | require only one security group to be present for the instance to be used for discovery | 'true' | no
| `elasticsearch_plugin_aws_ec2_ping_timeout` | how long to wait for existing EC2 nodes to reply during discovery | 3s |
| `elasticsearch_plugin_aws_access_key` | access key used for discovery. IAM will be used if not provided | none | no
| `elasticsearch_plugin_aws_secret_key` | secret key used for discovery. IAM will be used if not provided | none | no
| `elasticsearch_plugin_aws_region` | region required for discovery | none | no
| `elasticsearch_action_auto_create_index` | create index if it doesn't exist automatically | 'true' |
| `elasticsearch_action_disable_delete_all_indices` | disable allowing a single call to delete all indices | 'true' |
| `elasticsearch_conf_additional` | hash of additional configuration key: value items | none | no

*although no value is set by default, Elasticsearch uses built-in defaults

Dependencies
------------

None

Example Playbook
----------------

This example installs Elasticsearch 1.4.2 using S3 to the default directories, using the default settings included in the role.

    - hosts: servers
      roles:
         - { role: elasticsearch,
                  elasticsearch_version: 1.4.2,
                  elasticsearch_install_path: /opt,
                  elasticsearch_s3_bucket: sps-build-deploy,
                  elasticsearch_s3_base: /non-sps/elasticsearch/elasticsearch/,
                  elasticsearch_java_home: /opt/java/java8,
                  elasticsearch_state: present
                }

Author Information
------------------

Ryan O'Keeffe (okeefferd@gmail.com)
