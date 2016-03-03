# Ansible Galera (OpenStack ready)
This module provides a support for the installation of Galera cluster.

Two repositories will be added to the packaging system:
- MariaDB (MariaDB, Galera)
- Percona (xtrabackup)

Supported distributions:
- CentOS 7.x
- RedHat EL 7.x
- Debian 8.x

Supported functionalities:
- Firewalld (iptables)
- SELinux
- Pacemaker

Pacemaker support provides clustercheck tool and the needed configuration.  
When Pacemaker support is define, Galera cluster will be stopped at the end of the configuration.

## Requirements
This module needs at least 3 nodes and Ansible 2.0.

## Role Variables
If ``galera_reset_cluster`` is set to true, all databases will be erased.

### CONFIG
```
### MARIADB
master: ctrl01
mariadb_bind_address: 0.0.0.0
mariadb_port: 3306
mariadb_max_connections: 4096
mariadb_query_cache_size: 0
mariadb_default_storage_engine: InnoDB
mariadb_maintenance_password: I3uL6AqJLHInv85x
mariadb_root_password: 3248ew7dsYUG762
mariadb_hosts_allow: 192.168.%
mariadb_datadir: /var/lib/mysql

### GALERA
galera_node_address: "{{ ansible_eth0.ipv4.address }}"
galera_pacemaker_support: false
galera_clustercheck_user: clustercheck
galera_clustercheck_password: Y3aH1l0ved2CH3CK
galera_reset_cluster: false
galera_selinux: true
galera_firewalld: true
galera_cluster_name: uoi-sql-cluster
galera_sst_method: xtrabackup-v2
galera_sst_user: sst-replication
galera_sst_password: gr34tp4ss0rd
galera_cluster_nodes:
  - ctrl01
  - ctrl02
  - ctrl03
galera_provider_options: 'pc.ignore_quorum=true; gcache.size=1G'
galera_retry_autocommit: 10
galera_slave_threads: 8
```

### VARIABLES
Because the module support RedHat and Debian distributions like, we have to define some values depending of the family.
```
### REDHAT
# file: roles/galera/vars/RedHat.yml
galera_packages:
  - MariaDB-server
  - percona-xtrabackup
  - socat
  - MySQL-python
  - percona-toolkit
  - galera
  - policycoreutils-python
  - checkpolicy
  - xinetd
mariadb_svc_name: mariadb
mariadb_config: my.cnf.d/server.cnf
galera_provider: /usr/lib64/galera/libgalera_smm.so
percona_package: https://www.percona.com/redir/downloads/percona-release/redhat/latest/percona-release-0.1-3.noarch.rpm
```
```
### DEBIAN
# file: roles/galera/vars/Debian.yml
galera_packages:
  - mariadb-server
  - xtrabackup
  - socat
  - python-mysqldb
  - percona-toolkit
  - xinetd
mariadb_svc_name: mysql
mariadb_config: mysql/conf.d/galera.cnf
galera_provider: /usr/lib/galera/libgalera_smm.so
```
```
## MAIN
# file: roles/galera/vars/main.yml
galera_xinet_port: 9200

# FIREWALL PORTS
fw_mysql: "{{ mariadb_port }}"
fw_mysql_ist: 4568
fw_mysql_sst: 4444
fw_galera: 4567
fw_xinetd: "{{ galera_xinet_port }}"
```

## Dependencies
None.

## Example Playbook
```
master: node1
mariadb_bind_address: 0.0.0.0
mariadb_max_connections: 4096
mariadb_maintenance_password: I3uL6AqJLHInv85x
mariadb_root_password: 3248ew7dsYUG762
mariadb_hosts_allow: 10.0.%

galera_node_address: "{{ ansible_eth0.ipv4.address }}"
galera_pacemaker_support: false
galera_clustercheck_user: clustercheck
galera_clustercheck_password: Y3aH1l0ved2CH3CK
galera_cluster_name: uoi-sql-cluster
galera_sst_password: gr34tp4ss0rd
galera_cluster_nodes:
  - node-1
  - node-2
  - node-3
  - node-4
  - node-5
```

## galera-status
When the deployment is over, Please run ``galera-status`` command to get a clear status of Galera cluster.
```
# galera-status
```

## License
Apache

## Author Information
This role was created in 2016 by Gaëtan Trellu (goldyfruit).
