# infrared-scale

This project is an ansible based wrapper around [infrared](https://github.com/redhat-openstack/infrared) which can be used to deploy a TripleO/Director based OpenStack deployment at scale.

infrared-scale provides the automation and templates to deploy and tune a large OpenStack environment deployed using Virtual Machines on a hypervisor. The virsh networking and overcloud templates provided by infrared-scale are large enough to provision and deploy 100s of nodes of OpenStack. By deploying OpenStack at scale using infrared-scale, one can find out bugs/limitations in the TripleO installer.

Infrared spawns required number of VMs to act as undercloud, controller and compute nodes on the hypervisor, installs the undercloud and overcloud. Hence, by using infrared/infrared-scale, one can spawn a large number of VMs on the hypervisor and deploy OpenStack at scale on these VMs.

## Requirements

infrared-scale only needs ansible to work. ansible >= v2.5 is required.. All other requirements are installed as a part of infared installation. You must have passwordless-authentication and acces to the hypervisor you want launch VMs on from the node running infrared-scale(can be achieved using ssh-copy-id)
## Getting Started

infrared-scale is designed to install and orchestrate infrared from you local machine. You can use any machine you want to run infrared-scale from, by cloning infraredonto that machine and running it from there.

The virsh networking files required to create networks on the hypervisor large enough to accomodate enough VMs for scale deployments are provided [here](ansible/roles/infrared-provision/virsh-network) and are copied by infrared-scale into the appropriate path for use by infrared. 

Some variables like the version of OpenStack to be installed, build, name of the hypervisor and ssh-key file location need to be declared [here](ansible/vars/main.yml)as they are used by more than one role and hence haven't been made role specific.

Based on you hypervisor capacity and the number of VMs (OpenStack nodes) you want to scale to, you can adjust the number of VMs of each type to be provisioned and the memory/cpu requirements [here](ansible/roles/infrared-provision/vars/main.yml). If you do not provide any values, some safe defaults provided [here](ansible/roles/infrared-provision/defaults/main.yml) will be used.

The parameters to be applied in the undercloud.conf for undercloud installation suited for scale deployments are given [here](ansible/roles/infrared-undercloud/defaults/main.yml). You can change these by giving your own values in ansible/roles/infrared-undercloud/vars. Defaults work well, so you can leave them as is

The overcloud network templates needed to deploy a large overcloud are present [here](overcloud-network/virt-scale) and are copied by infrared to the appropriate path for use by infrared.

Set the environment variable workspace to the location you want to download and instll infrared to:

```
export WORKSPACE=/some/path
```

After declaring needed ansible and environment variables, run the playbook to deploy an overcloud at scale by,
```
ansible-playbook deploy-scale-cloud.yml --tags setup
```

Cleanup the hypervisor (delete cloud and VMs):
```
ansible-playbook deploy-scale-cloud.yml --tags teardown
```
