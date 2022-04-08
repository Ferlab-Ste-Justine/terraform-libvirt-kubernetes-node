# About

This package provisions a node (master or worker) that you can install kubernetes on (validated with Kubespray)

The implementation is currently quite minimalistic and beyond enable ipv4 forwarding, don't really do any kubernetes-specific setup.

# Usage

## Input

The module takes the following variables as input:

- **name**: Name of the load balancer vm
- **vcpus**: Number of vcpus to assign to the load balancer. Defaults to 2.
- **memory**: Amount of memory to assign to the bastion in MiB. Defaults to 8192 (8 GiB).
- **volume_id**: Id of the disk volume to attach to the vm
- **network_id**: Id of the libvirt network to connect the vm to if you plan on connecting the vm to a libvirt network
- **ip**: Ip of the vm if you opt to connect it to a libvirt network.
- **mac**: Mac address of the vm if you opt to connect it to a libvirt network. If none is passed, a random one will be generated.
- **macvtap_interfaces**: List of macvtap interfaces to connect the vm to if you opt for macvtap interfaces instead of a libvirt network. Each entry in the list is a map with the following keys:
  - **interface**: Host network interface that you plan to connect your macvtap interface with.
  - **prefix_length**: Length of the network prefix for the network the interface will be connected to. For a **192.168.1.0/24** for example, this would be 24.
  - **ip**: Ip associated with the macvtap interface. 
  - **mac**: Mac address associated with the macvtap interface
  - **gateway**: Ip of the network's gateway for the network the interface will be connected to.
  - **dns_servers**: Dns servers for the network the interface will be connected to. If there aren't dns servers setup for the network your vm will connect to, the ip of external dns servers accessible accessible from the network will work as well.
- **cloud_init_volume_pool**: Name of the volume pool that will contain the cloud-init volume of the vm.
- **cloud_init_volume_name**: Name of the cloud-init volume that will be generated by the module for your vm. If left empty, it will default to ``<vm name>-cloud-init.iso``.
- **ssh_admin_user**: Username of the default sudo user in the image. Defaults to **ubuntu**.
- **admin_user_password**: Optional password for the default sudo user of the image. Note that this will not enable ssh password connections, but it will allow you to log into the vm from the host using the **virsh console** command.
- **ssh_admin_public_key**: Public part of the ssh key that will be used to login as the admin on the vm
- **chrony**: Optional chrony configuration for when you need a more fine-grained ntp setup on your vm. It is an object with the following fields:
  - **enabled**: If set the false (the default), chrony will not be installed and the vm ntp settings will be left to default.
  - **servers**: List of ntp servers to sync from with each entry containing two properties, **url** and **options** (see: https://chrony.tuxfamily.org/doc/4.2/chrony.conf.html#server)
  - **pools**: A list of ntp server pools to sync from with each entry containing two properties, **url** and **options** (see: https://chrony.tuxfamily.org/doc/4.2/chrony.conf.html#pool)
  - **makestep**: An object containing remedial instructions if the clock of the vm is significantly out of sync at startup. It is an object containing two properties, **threshold** and **limit** (see: https://chrony.tuxfamily.org/doc/4.2/chrony.conf.html#makestep)