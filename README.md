# ansible-cloud_utils
Ansible utilities for performing operations against public and private "clouds".

## Playbooks
Playbooks provided by this project.

### enable-chrony.yml
Enables `chrony` and disables `ntp`.

### idm-register-ipa-client.yml
Registers an IPA client with an IdM or IPA server.

#### Options
| parameter             | requried | default | comments
|-----------------------|----------|---------|---------
| idm\_server           | yes      |         | The IdM/IPA server to regsiter the IPA client with
| idm\_domain           | yes      |         | The IdM/IPA domain to register the IPA client with
| idm\_enroll\_user     | yes      |         | The IdM/IPA user to enroll the IPA client with
| idm\_enroll\_password | yes      |         | The IdM/IPA password to enroll the IPA client with
| idm\_register\_force  | no       | False   | If `true` and IPA client is already registered it will be unregistered.

#### Notes
* Does not configure NTP

### configure\_vm\_network\_and\_ip.yml
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
| parameter                         | required     | default                        | choices                                       | comments
|-----------------------------------|--------------|--------------------------------|-----------------------------------------------|---------------------------------------------
| ansible\_manage\_etc\_hosts       | no           | false                          | true, false, yes, no                          | Flag to control if /etc/hosts is updated with newly configured ip address
| vm\_network\_ip4                  | yes          |                                |                                               | IP4 address to set for the VM
| vm\_network\_ip4\_netmask\_prefix | yes          |                                |                                               | IP4 address netmask prefex to set for the VM
| vm\_network\_gw4                  | yes          |                                |                                               | IP4 gateway to set for the VM
| vm\_network\_dns4                 | no           |                                |                                               | List of IP4 DNS hosts to set for the VM
| vm\_network\_ifname               | no           | `eth0` for `RHEV`, `ens192` for `VMware` |                                     | Ethernet interface name to set for the VM
| vm\_network\_conn\_name           | no           | System {{ vm_network_ifname }} |                                               | Ethernet connection name to set for the vm
| virt\_api\_bastion                | no           | localhost                      |                                               | Bastion host to use to do API calls to the virtulization provider.
| virt\_api\_insecure               | no           | False                          | True/False                                    | Whether the connection to the virtualization provider API is insecure or not, aka using trusted certificates.
| virt\_network                     | yes          |                                | Valid networks on the virtualization provider | Virtualization provider network to set for the VM
| ovirt\_url                        | If oVirt/RHV |                                |                                               | oVirt/RHV url for API calls
| ovirt\_username                   | If oVirt/RHV |                                |                                               | oVirt/RHV username for API calls
| ovirt\_password                   | If oVirt/RHV |                                |                                               | oVirt/RHV passwrod for API calls
| vsphere\_hostname                 | If vSphere   |                                |                                               | vSphere hostname for API calls
| vsphere\_username                 | If vSphere   |                                |                                               | vSphere username for API calls
| vsphere\_password                 | If vSphere   |                                |                                               | vSphere password for API calls
| vsphere\_datacenter               | If vSphere   |                                |                                               | vSphere datacenter for API calls. NOTE: attempted to determine this dynamically but could not find a way.

### optimize\_kernel\_scheduler.yml
[What is the suggested I/O scheduler to improve disk performance when using Red Hat Enterprise Linux with virtualization?](https://access.redhat.com/solutions/5427).  The following playbook configures noop for the IO queue scheduler kernel parameter for RHEL VMs on VMware infrastructure per [How to use the Noop IO Scheduler](https://access.redhat.com/solutions/109223).

#### Notes
* A backup of the grub configuration is made when changed.
* This playbook does not perform the required reboot to enable this paramater.
