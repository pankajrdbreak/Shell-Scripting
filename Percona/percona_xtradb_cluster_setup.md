# Percona Xtradb Cluster57 Setup in Ubuntu 20 

Percona cluster is used for mysql replication. In this tutorial we will se how to install and configure percona cluster replication in 3 nodes

## Prerequisite 
1) Node 1 (Ubuntu 20)
--> hostname : knode         --> ip : 192.168.246.129

2) Node 2 (Ubuntu 20)
--> hostname : knode2        --> ip : 192.168.246.130

3) Node 3 (Ubuntu 20)
--> hostname : knode3        --> ip :


Note : Don't install mysql or any other package start with fresh machines


## Procedure

#### Lets start on knode (Node 1)

##### Note : Do the all steps in super user mode (sudo su)

1) Add and install percona repository
```
wget https://repo.percona.com/apt/percona-release_latest.bionic_all.deb

dpkg -i percona-release_latest.bionic_all.deb
```
Here i have selected percona-release_latest.bionic_all.deb from https://repo.percona.com/apt/

2) Update the system
```
apt-get update
```
3) Now we will install percona-xtradb-cluster with mysql 5.7 version
```
apt-get install -y percona-xtradb-cluster-57
```
4) Now stop the mysql service
```
systemctl stop mysql
```
5) Now configuration part comes in. We have to edit my.cnf file and do some configuration
```
vim /etc/mysql/my.cnf
```
Add the below code to my.cnf file
```
[mysqld]
wsrep_provider=/usr/lib/libgalera_smm.so
wsrep_cluster_name=democluster
wsrep_cluster_address=gcomm://
wsrep_node_name=knode
wsrep_node_address=192.168.246.129
wsrep_sst_method=xtrabackup-v2
wsrep_sst_auth=pankaj:pankaj123
pxc_strict_mode=ENFORCING
binlog_format=ROW
default_storage_engine=InnoDB
innodb_autoinc_lock_mode=2
```
Finally the file will lokk like below
```
#
# The Percona XtraDB Cluster 5.7 configuration file.
#
#
# * IMPORTANT: Additional settings that can override those from this file!
#   The files must end with '.cnf', otherwise they'll be ignored.
#   Please make any edits and changes to the appropriate sectional files
#   included below.
#
!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/percona-xtradb-cluster.conf.d/
[mysqld]
wsrep_provider=/usr/lib/libgalera_smm.so
wsrep_cluster_name=democluster
wsrep_cluster_address=gcomm://
wsrep_node_name=knode
wsrep_node_address=192.168.246.129
wsrep_sst_method=xtrabackup-v2
wsrep_sst_auth=pankaj:pankaj123
pxc_strict_mode=ENFORCING
binlog_format=ROW
default_storage_engine=InnoDB
innodb_autoinc_lock_mode=2
~                                                                                                                                                                                     
~                                                                                                                                                                                     
~                                                                                                                                                                                     
~                                                                                                                                                                                     
~                                                                                                                                                                                     
~                                                                                                                                                                                     
~                                                                                                                                                                                     
~                                                                                                                                                                                     
~                                                                                                                                                                                                                                                                                                                                                                    
"/etc/mysql/my.cnf" 23L, 744C  
```
In the file 
wsrep_cluster_name = your cluster name whatever you want
wsrep_node_name    = your node name
wsrep_node_address = ip address of your node
wsrep_sst_auth     = this mysql user we will going to create on 1st node as a replication user

6) Save and close the file
7) Now we can start the service
```
systemctl start mysql
```
8) Now we have to cretae that replication user
```
mysql -uroot -p -e "create user pankaj@localhost identified by 'pankaj123'"
mysql -uroot -p -e "grant reload, replication client, process, lock tables on *.* to pankaj@localhost"
mysql -uroot -p -e "flush privileges"
```
9) Now you can login the mysql server and check the status of cluster
```
mysql -u root -p
mysql> show status like '%wsrep%';
+----------------------------------+-------------------------------------------+
| Variable_name                    | Value                                     |
+----------------------------------+-------------------------------------------+
| wsrep_local_state_uuid           | bc11ec2a-b31e-11ec-82b2-671aeae5b84f      |
| wsrep_protocol_version           | 9                                         |
| wsrep_last_applied               | 3                                         |
| wsrep_last_committed             | 3                                         |
| wsrep_replicated                 | 0                                         |
| wsrep_replicated_bytes           | 0                                         |
| wsrep_repl_keys                  | 0                                         |
| wsrep_repl_keys_bytes            | 0                                         |
| wsrep_repl_data_bytes            | 0                                         |
| wsrep_repl_other_bytes           | 0                                         |
| wsrep_received                   | 10                                        |
| wsrep_received_bytes             | 1053                                      |
| wsrep_local_commits              | 0                                         |
| wsrep_local_cert_failures        | 0                                         |
| wsrep_local_replays              | 0                                         |
| wsrep_local_send_queue           | 0                                         |
| wsrep_local_send_queue_max       | 1                                         |
| wsrep_local_send_queue_min       | 0                                         |
| wsrep_local_send_queue_avg       | 0.000000                                  |
| wsrep_local_recv_queue           | 0                                         |
| wsrep_local_recv_queue_max       | 2                                         |
| wsrep_local_recv_queue_min       | 0                                         |
| wsrep_local_recv_queue_avg       | 0.200000                                  |
| wsrep_local_cached_downto        | 0                                         |
| wsrep_flow_control_paused_ns     | 0                                         |
| wsrep_flow_control_paused        | 0.000000                                  |
| wsrep_flow_control_sent          | 0                                         |
| wsrep_flow_control_recv          | 0                                         |
| wsrep_flow_control_interval      | [ 141, 141 ]                              |
| wsrep_flow_control_interval_low  | 141                                       |
| wsrep_flow_control_interval_high | 141                                       |
| wsrep_flow_control_status        | OFF                                       |
| wsrep_flow_control_active        | false                                     |
| wsrep_flow_control_requested     | false                                     |
| wsrep_cert_deps_distance         | 0.000000                                  |
| wsrep_apply_oooe                 | 0.000000                                  |
| wsrep_apply_oool                 | 0.000000                                  |
| wsrep_apply_window               | 0.000000                                  |
| wsrep_apply_waits                | 0                                         |
| wsrep_commit_oooe                | 0.000000                                  |
| wsrep_commit_oool                | 0.000000                                  |
| wsrep_commit_window              | 0.000000                                  |
| wsrep_local_state                | 4                                         |
| wsrep_local_state_comment        | Synced                                    |
| wsrep_cert_index_size            | 0                                         |
| wsrep_cert_bucket_count          | 22                                        |
| wsrep_gcache_pool_size           | 1456                                      |
| wsrep_causal_reads               | 0                                         |
| wsrep_cert_interval              | 0.000000                                  |
| wsrep_open_transactions          | 0                                         |
| wsrep_open_connections           | 0                                         |
| wsrep_ist_receive_status         |                                           |
| wsrep_ist_receive_seqno_start    | 0                                         |
| wsrep_ist_receive_seqno_current  | 0                                         |
| wsrep_ist_receive_seqno_end      | 0                                         |
| wsrep_incoming_addresses         | 192.168.246.130:3306,192.168.246.129:3306 |
| wsrep_cluster_weight             | 2                                         |
| wsrep_desync_count               | 0                                         |
| wsrep_evs_delayed                |                                           |
| wsrep_evs_evict_list             |                                           |
| wsrep_evs_repl_latency           | 0/0/0/0/0                                 |
| wsrep_evs_state                  | OPERATIONAL                               |
| wsrep_gcomm_uuid                 | bee5350c-b320-11ec-a064-ef0cda3c2b71      |
| wsrep_gmcast_segment             | 0                                         |
| wsrep_cluster_conf_id            | 4                                         |
| wsrep_cluster_size               | 2                                         |
| wsrep_cluster_state_uuid         | bc11ec2a-b31e-11ec-82b2-671aeae5b84f      |
| wsrep_cluster_status             | Primary                                   |
| wsrep_connected                  | ON                                        |
| wsrep_local_bf_aborts            | 0                                         |
| wsrep_local_index                | 1                                         |
| wsrep_provider_name              | Galera                                    |
| wsrep_provider_vendor            | Codership Oy <info@codership.com>         |
| wsrep_provider_version           | 3.55(r8b6416d)                            |
| wsrep_ready                      | ON                                        |
+----------------------------------+-------------------------------------------+
75 rows in set (0.01 sec)
```
#### Note : 
1) If firewall is enabled we have to allow some ports on all the nodes 
```
systemctl status ufw
ufw allow 4567/tcp
ufw allow 4567/udp
ufw allow 4444/tcp
systemctl restart ufw
```
2) Also cross check the uuid on all the nodes and make sure its same like 1st node if not then please edit the file given below and change the uuid
```
vim /var/lib/mysql/grastate.dat
```
# GALERA saved state
version: 2.1
uuid:    bc11ec2a-b31e-11ec-82b2-671aeae5b84f
seqno:   -1
safe_to_bootstrap: 0
```
3) If you are on aws ec2 instances then add all traffic rule in security group

##### Thats it our first node is ready and now we can add more nodes to join the cluster



#### Lets start on knode2 (Node 2)

##### Note : Do the all steps in super user mode (sudo su)

1) Add and install percona repository
```
wget https://repo.percona.com/apt/percona-release_latest.bionic_all.deb

dpkg -i percona-release_latest.bionic_all.deb
```
Here i have selected percona-release_latest.bionic_all.deb from https://repo.percona.com/apt/

2) Update the system
```
apt-get update
```
3) Now we will install percona-xtradb-cluster with mysql 5.7 version
```
apt-get install -y percona-xtradb-cluster-57
```
4) Now stop the mysql service
```
systemctl stop mysql
```
5) Now configuration part comes in. We have to edit my.cnf file and do some configuration
```
vim /etc/mysql/my.cnf
```
Add the below code to my.cnf file
```
[mysqld]
wsrep_provider=/usr/lib/libgalera_smm.so
wsrep_cluster_name=democluster
wsrep_cluster_address=gcomm://192.168.246.129,192.168.246.130
wsrep_node_name=knode2
wsrep_node_address=192.168.246.130
wsrep_sst_method=xtrabackup-v2
wsrep_sst_auth=pankaj:pankaj123
pxc_strict_mode=ENFORCING
binlog_format=ROW
default_storage_engine=InnoDB
innodb_autoinc_lock_mode=2
```
Finally the file will lokk like below
```
#
# The Percona XtraDB Cluster 5.7 configuration file.
#
#
# * IMPORTANT: Additional settings that can override those from this file!
#   The files must end with '.cnf', otherwise they'll be ignored.
#   Please make any edits and changes to the appropriate sectional files
#   included below.
#
!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/percona-xtradb-cluster.conf.d/
[mysqld]
wsrep_provider=/usr/lib/libgalera_smm.so
wsrep_cluster_name=democluster
wsrep_cluster_address=gcomm://192.168.246.129,192.168.246.130
wsrep_node_name=knode2
wsrep_node_address=192.168.246.130
wsrep_sst_method=xtrabackup-v2
wsrep_sst_auth=pankaj:pankaj123
pxc_strict_mode=ENFORCING
binlog_format=ROW
default_storage_engine=InnoDB
innodb_autoinc_lock_mode=2
~                                                                                                                                                                                     
~                                                                                                                                                                                     
~                                                                                                                                                                                     
~                                                                                                                                                                                     
~                                                                                                                                                                                     
~                                                                                                                                                                                     
~                                                                                                                                                                                     
~                                                                                                                                                                                     
~                                                                                                                                                                                                                                                                                                                                                                    
"/etc/mysql/my.cnf" 23L, 744C  
```
6) Save and close the file
7) Now we can start the service
```
systemctl start mysql
```
8) Now you can see the logs if you want to check
9) Also on first node you can repeat 9th step and see wsrep_cluster_size is 2 now , it means another node is added to the cluster
10) Now to see whether actuall replication is hapening or not you can ceate db,table and insert data into it from any node and both the node should be synced and replicate that data.


###### Like same we have to add 3rd node to the cluster just we have to add ip in my.cnf of all nodes and have to change below 2 parameters on 3rd node
```
wsrep_node_name=knode3
wsrep_node_address=192.168.246.131
```


# Thanks for reading..!

