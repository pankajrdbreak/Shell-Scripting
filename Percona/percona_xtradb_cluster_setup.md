# Percona Xtradb Cluster57 Setup in Ubuntu 20 

Percona cluster is used for mysql replication. In this tutorial we will se how to install and configure percona cluster replication in 3 nodes

# Prerequisite 
1) Node 1 (Ubuntu 20)
--> hostname : knode         --> ip : 

2) Node 2 (Ubuntu 20)
--> hostname : knode2        --> ip : 

3) Node 3 (Ubuntu 20)
--> hostname : knode3        --> ip :


Note : Don't install mysql or any other package start with fresh machines


# Procedure

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
wsrep_cluster_address=gcomm://192.168.246.129,192.168.246.130
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
wsrep_cluster_address=gcomm://192.168.246.129,192.168.246.130
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
