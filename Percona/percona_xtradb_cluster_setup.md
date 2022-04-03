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

##### Do the all steps in super user mode (sudo su)

1) Add and install percona repository
```
wget https://repo.percona.com/apt/percona-release_latest.bionic_all.deb

dpkg -i percona-release_latest.bionic_all.deb
```
Here i have selected percona-release_latest.bionic_all.deb from https://repo.percona.com/apt/

2) 
