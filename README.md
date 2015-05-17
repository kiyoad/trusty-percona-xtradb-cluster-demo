# trusty-percona-xtradb-cluster-demo
Percona XtraDB Cluster demo for Ubuntu trusty by Ansible and Vagrant.

## Abstract

This Vagrantfile will deploy the Percona XtraDB Cluster on Ubuntu 14.04 LTS virtual machines with VirtualBox.

## Requirements

My development environment is shown below. I think that Ubuntu LinuxBox also works.

    $ cat /etc/redhat-release
    CentOS Linux release 7.1.1503 (Core)
    $ uname -a
    Linux zouk.local 3.10.0-229.4.2.el7.x86_64 #1 SMP Wed May 13 10:06:09 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
    $ cat /proc/meminfo | head -6
    MemTotal:       16243556 kB
    MemFree:          153060 kB
    MemAvailable:    7363000 kB
    Buffers:            4076 kB
    Cached:          7484732 kB
    SwapCached:        15380 kB
    $ vboxmanage --version
    4.3.28r100309
    $ vagrant --version
    Vagrant 1.7.2
    $ ansible --version
    ansible 1.9.1
      configured module search path = None
    $ vagrant box list
    ubuntu/trusty64 (virtualbox, 0)

My Vagrant base box 'ubuntu/trusty64' obtained from the following.
https://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box

## How to use

1. run `vagrant up` and `vagrant reload`
1. login to *starter* by `vagrant ssh starter`
1. Setup the Percona XtraDB Cluster by using script like this.

    ```
    vagrant@starter:~$ ./setup-pxc.sh
    ```

1. The following message is a successful example.

    ```
    vagrant@starter:~$ ./setup-pxc.sh
    + ssh sv1 sudo service mysql bootstrap-pxc
    The authenticity of host 'sv1.local (10.0.0.61)' can't be established.
    ECDSA key fingerprint is 48:10:3c:4e:98:ca:f2:2b:bd:5f:69:54:6e:84:39:e7.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added 'sv1.local,10.0.0.61' (ECDSA) to the list of known hosts.
     * Bootstrapping Percona XtraDB Cluster database server mysqld
       ...done.
    ++ mktemp /tmp/setup-pxc-XXXXX
    + sqlfile=/tmp/setup-pxc-2MVxW
    + cat
    + scp /tmp/setup-pxc-2MVxW sv1:/tmp/setup-pxc-2MVxW
    setup-pxc-2MVxW                                       100%  390     0.4KB/s   00:00
    + set +e
    + ssh sv1 mysql -uroot -N -s -e '"source' '/tmp/setup-pxc-2MVxW"'
    + set -e
    + rm /tmp/setup-pxc-2MVxW
    + ssh sv1 rm /tmp/setup-pxc-2MVxW
    + ssh sv2 sudo service mysql start
    The authenticity of host 'sv2.local (10.0.0.62)' can't be established.
    ECDSA key fingerprint is 0c:e6:70:72:58:9d:3a:4e:23:b7:05:db:6e:1b:0a:13.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added 'sv2.local,10.0.0.62' (ECDSA) to the list of known hosts.
     * Starting MySQL (Percona XtraDB Cluster) database server mysqld
     * State transfer in progress, setting sleep higher mysqld
       ...done.
    + ssh sv3 sudo service mysql start
    The authenticity of host 'sv3.local (10.0.0.63)' can't be established.
    ECDSA key fingerprint is ea:72:b0:6e:23:3e:26:30:af:82:33:48:c4:4d:66:1e.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added 'sv3.local,10.0.0.63' (ECDSA) to the list of known hosts.
     * Starting MySQL (Percona XtraDB Cluster) database server mysqld
     * State transfer in progress, setting sleep higher mysqld
       ...done.
    vagrant@starter:~$
    ```

1. My Percona XtraDB Cluster version is show below.(see https://www.percona.com/doc/percona-xtradb-cluster/5.6/index.html )

    ```
    vagrant@starter:~$ mysql --version
    mysql  Ver 14.14 Distrib 5.6.22-72.0, for debian-linux-gnu (x86_64) using  EditLine wrapper
    vagrant@starter:~$ ssh sv1 mysql --version
    mysql  Ver 14.14 Distrib 5.6.22-72.0, for debian-linux-gnu (x86_64) using  EditLine wrapper
    ```

## Restrictions

1. `./setup-pxc.sh` can not be used more than once after provision.

## Memo

1. After the stop of all of cluster servers(sv1,sv2,sv3), you need to start the Percona XtraDB Cluster manually after the restart them as follows.

    ```
    vagrant@starter:~$ ssh sv1 sudo service mysql bootstrap-pxc
     * Bootstrapping Percona XtraDB Cluster database server mysqld
       ...done.
    vagrant@starter:~$ ssh sv2 sudo service mysql start
     * Starting MySQL (Percona XtraDB Cluster) database server mysqld
       ...done.
    vagrant@starter:~$ ssh sv3 sudo service mysql start
     * Starting MySQL (Percona XtraDB Cluster) database server mysqld
       ...done.
    vagrant@starter:~$
    ```
