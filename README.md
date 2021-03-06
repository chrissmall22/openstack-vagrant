openstack-vagrant
=================

Vagrant files for OpenStack development and experimentation

# Installation

You can download the Vagrant client for your system from [here](http://www.vagrantup.com/downloads.html)

# Usage

Vagrant is a tool for creating development/test environments.
The environment created for this project is as follows:

+ 1 x DevStack Control/Compute VM for ODL
+ 1 x DevStack Compute VM for ODL
+ 1 x DevStack with ML2 (without ODL Mechanism Driver)
+ 1 x Mininet VM

It's assumed you already have an OVSDB development environment set up.

1. Run `mvn clean install`
2. Run `vagrant up`
3. Start the controller (in distirbution/target/opendaylight/...)

> Note: It's assumed that the subnet 192.168.50.x/24 is not in use
> Your PC is 192.168.50.1. If you would like to change the addressing,
> you will need to edit the Vagrantfile and hosts.json

Follow the instructions below for Devstack and Mininet

## DevStack VMs

There are two DevStack VMs. One combined control/compute node and a variable number of dedicated compute nodes.
The following steps are performed when the VM is created or when `vagrant provision` is run.

- Check dependencies are installed
- Clone the devstack repository to `devstack`
- Generate a `local.conf` file

To start DevStack

    vagrant ssh devstack-control
    # or vagrant ssh devstack-compute
    cd devstack
    ./stack.sh

> Note: To start more than one compute node
>       export DEVSTACK_NUM_COMPUTE_NODES=3
>       vagrant up

## Mininet

There is a single VM provided for running mininet.
This is for testing that does not require OpenStack Neutron.

The following steps are performed when the VM is created or when `vagrant provision` is run.
- Check dependencies are installed
- Clone the mininet repository
- Install mininet and Open vSwitch

To start Mininet

    vagrant ssh mininet
    sudo mn

# Port Forwarding

The following ports are forwarded:

localhost:8080 -> opendaylight:8080
localhost:8000 -> opendaylight:8000
localhost:8081 -> devstack-control:8080
