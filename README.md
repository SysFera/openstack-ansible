# OpenStack on Ansible with Vagrant

This repository contains script that will deploy OpenStack into Vagrant virtual
machines. These scripts are based on the [Official OpenStack
Docmentation](http://docs.openstack.org/), havana release, except where
otherwise noted.

See also [Vagrant, Ansible and OpenStack on your lyumop]
(http://www.slideshare.net/lorinh/vagrant-ansible-and-openstack-on-your-lyumop)
on SlideShare, though this refers to a much older version of this repo and so is 
now out of date.

## Install prereqs

You'll need to install:

* [Vagrant](http://vagrantup.com)
* [Ansible](http://ansible.github.com)
* [python-netaddr](https://pypi.python.org/pypi/netaddr/)
* [python-novaclient](https://pypi.python.org/pypi/python-novaclient) (recommended)

To get started with the development branch of Ansible, install the prerequisites,
grab the git repo and source the appropriate file to set your environment
variables, no other installation is required:

	sudo pip install paramiko PyYAML Jinja2 netaddr python-novaclient
	git clone git://github.com/ansible/ansible.git
	cd ./ansible
	source ./hacking/env-setup


## Get an Ubuntu 12.04 (precise) Vagrant box

Download a 64-bit Ubuntu Vagrant box:

	vagrant box add precise64 http://lyte.id.au/vagrant/sl6-64-lyte.box

## Grab this repository

This repository uses a submodule that contains some custom Ansible modules for
OpenStack, so there's an extra command required after cloning the repo:

    git clone http://github.com/openstack-ansible/openstack-ansible.git
    cd openstack-ansible
    git submodule update --init

## Bring up the cloud

    make

This will boot three VMs (controller, network, storage, and a compute node),
install OpenStack, and attempt to boot a test VM inside of OpenStack.

If everything works, you should be able to ssh to the instance from any
of your vagrant hosts:

 * username: `cirros`
 * password: `cubswin:)`

Note: You may get a "connection refused" when attempting to ssh to the instance.
It can take several minutes for the ssh server to respond to requests, even
though the cirros instance has booted and is pingable.

## Vagrant hosts

The hosts for the standard configuration are:

 * 10.1.0.2 (our cloud controller)
 * 10.1.0.3 (compute node #1)
 * 10.1.0.4 (the quantum network host)
 * 10.1.0.5 (the swift storage host)

You should be able to ssh to these VMs (username: `vagrant`, password:
`vagrant`). You can also authenticate  with the vagrant private key, which is
included here as the file `vagrant_private_key` (NOTE: git does not manage file
permissions, these must be set to using "chmod 0600 vagrant_private_key" or ssh
and ansible will fail with an error).

## Interacting with your cloud

You can interact with your cloud directly from your desktop, assuming that you
have the [python-novaclient](http://pypi.python.org/pypi/python-novaclient/)
installed.

Note that the openrc file will be created on the controller by default.


## Work In Progress : SysFera workaround.


controller :
paquet openstack-keystone
	python-keystone

Une fois installée il faut executer keystone-manage pki_setup --keystone-user 163 --keystone-group 163

sudo ln -s /var/run/mysqld/mysqld.sock /var/lib/mysql/mysql.sock

installer sur storage :  

::

rsync :  cat /etc/init.d/rsync
#! /bin/sh
#
# chkconfig:   2345 50 50
# description: The rsync daemon

# source function library
 . /etc/rc.d/init.d/functions

PROG='/usr/bin/rsync'
BASE=${0##*/}

# Adapt the --config parameter to point to your rsync daemon configuration
# The config file must contain following line:
#  pid file = /var/run/<filename>.pid
# Where <filename> is the filename of the init script (= this file)
OPTIONS="--daemon --config=/etc/rsyncd.conf"

case "$1" in
  start)
    echo -n $"Starting $BASE: "
    daemon --check $BASE $PROG $OPTIONS
    RETVAL=$?
    [ $RETVAL -eq 0 ] && touch /var/lock/subsys/$BASE
    echo
    ;;
  stop)
    echo -n $"Shutting down $BASE: "
    killproc $BASE
    RETVAL=$?
    [ $RETVAL -eq 0 ] && rm -f /var/lock/subsys/$BASE
    echo
    ;;
  restart|force-reload)
    $0 stop
    sleep 1
    $0 start
    ;;
  *)
    echo "Usage: $0 {start|stop|restart|force-reload}" >&2
    exit 1
    ;;
esac

exit 0


Sur storage 
/etc/init.d/quantum-server !

::

#! /bin/sh
#
# chkconfig:   2345 50 50
# description: The rsync daemon

# source function library
 . /etc/rc.d/init.d/functions

PROG='quantum-server'
BASE=${0##*/}

# Adapt the --config parameter to point to your rsync daemon configuration
# The config file must contain following line:
#  pid file = /var/run/<filename>.pid
# Where <filename> is the filename of the init script (= this file)
OPTIONS="--config-file /etc/neutron/neutron.conf &"
case "$1" in
  start)
    echo -n $"Starting $BASE: "
    daemon --check $BASE $PROG $OPTIONS
    RETVAL=$?
    [ $RETVAL -eq 0 ] && touch /var/lock/subsys/$BASE
    echo
    ;;
  stop)
    echo -n $"Shutting down $BASE: "
    killproc $BASE
    RETVAL=$?
    [ $RETVAL -eq 0 ] && rm -f /var/lock/subsys/$BASE
    echo
    ;;
  restart|force-reload)
    $0 stop
    sleep 1
    $0 start
    ;;
  *)
    echo "Usage: $0 {start|stop|restart|force-reload}" >&2
    exit 1
    ;;
esac

exit 0



copier le template rsyncd pour que ansible puisse faire restart

virer [enable rsync in /etc/default]


* :
Voir les /etc/default

storage Port : iptables -F rsync


====FAQ 
fatal: [controller] => {'msg': "file: /home/sysfera/soft/vagrant/openstack-ansible/services/cinder/templates/etc/cinder/cinder.conf, line number: 261, error: no filter named 'find_ip'", 'failed': True}
fatal: [controller] => {'msg': 'One or more items failed.', 'failed': True, 'changed': False, 'results': [{'msg': "file: /home/sysfera/soft/vagrant/openstack-ansible/services/cinder/templates/etc/cinder/cinder.conf, line number: 261, error: no filter named 'find_ip'", 'failed': True}]}

FATAL: all hosts have already failed -- aborting

il faut recopier les lib utils compilés cp -R services/swift/filter_plugins/ services/cinder/


=== 
Heat ne pase pas : probleme avec keystone. Il n'est pas capable de faire 

