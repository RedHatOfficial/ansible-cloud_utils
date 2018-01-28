# ansible-cloud_utils
Ansible utilities for performing operations against public and private "clouds".

## Playbooks
Playbooks provided by this project.

### configure_vm_network_and_ip.yml
Configures the virtualization provider network and IP for given hosts. This is useful if need migrate a VM from one vlan/subnet to another vlan/subnet by updating both the virtualization provider and destination host.

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
| vm_network_dns4               | yes          |                                |                                               | List of IP4 DNS hosts to set for the VM
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
