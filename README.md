# ansible-cloud_utils
Ansible utilities for performing operations against public and private "clouds".

## Playbooks
Playbooks provided by this project.

### configure_vm_network_and_ip.yml
Configures the virtualization provider network and IP for given hosts. This is useful if need migrate a VM from one vlan/subnet to another vlan/subnet by updating both the virtualization provider and destination host.

The concept is that this playbook is cloud provider agnostic and can figure out how to move a VM from one network to another on any cloud provider, staying on that same provider, based on gathering facts about the VM. Though currently it has only been tested and written for a couple providers, as need arises for more providers the playbook can be easly extended to handle more.

#### Tested With
* Red Hat Virtualization
  * 4.0.5
* VMware vCenter
  * 5.1.0

#### Assumptions
* Only handles one network interface
* Assumes migrating the first network interface
* target host facts are gathered so that `ansible_virtualization_type` is set
* `ansible_virtualization_type` in ['RHEV', 'VMware']

#### Options
| parameter                     | required     | default                        | choices                                       | comments
|-------------------------------|--------------|--------------------------------|-----------------------------------------------|---------------------------------------------
| vm_network_ip4                | yes          |                                |                                               | IP4 address to set for the VM
| vm_network_ip4_netmask_prefix | yes          |                                |                                               | IP4 address netmask prefex to set for the VM
| vm_network_gw4                | yes          |                                |                                               | IP4 gateway to set for the VM
| vm_network_dns4               | no           |                                |                                               | List of IP4 DNS hosts to set for the VM
| vm_network_ifname             | no           | `eth0` for `RHEV`, `ens192` for `VMware` |                                     | Ethernet interface name to set for the VM
| vm_network_conn_name          | no           | System {{ vm_network_ifname }} |                                               | Ethernet connection name to set for the vm
| virt_api_bastion              | no           | localhost                      |                                               | Bastion host to use to do API calls to the virtulization provider.
| virt_api_insecure             | no           | False                          | True/False                                    | Whether the connection to the virtualization provider API is insecure or not, aka using trusted certificates.
| virt_network                  | yes          |                                | Valid networks on the virtualization provider | Virtualization provider network to set for the VM
| ovirt_url                     | If oVirt/RHV |                                |                                               | oVirt/RHV url for API calls
| ovirt_username                | If oVirt/RHV |                                |                                               | oVirt/RHV username for API calls
| ovirt_password                | If oVirt/RHV |                                |                                               | oVirt/RHV passwrod for API calls
| vsphere_hostname              | If vSphere   |                                |                                               | vSphere hostname for API calls
| vsphere_username              | If vSphere   |                                |                                    | vSphere username for API calls
| vsphere_password              | If vSphere   |                                |                                               | vSphere password for API calls
| vsphere_datacenter            | If vSphere   |                                |                                               | vSphere datacenter for API calls. NOTE: attempted to determine this dynamically but could not find a way.

### optimize_kernel_scheduler.yml
[What is the suggested I/O scheduler to improve disk performance when using Red Hat Enterprise Linux with virtualization?](https://access.redhat.com/solutions/5427).  The following playbook configures noop for the IO queue scheduler kernel parameter for RHEL VMs on VMware infrastructure per [How to use the Noop IO Scheduler](https://access.redhat.com/solutions/109223).

#### Notes
* A backup of the grub configuration is made when changed.
* This playbook does not perform the required reboot to enable this paramater.
