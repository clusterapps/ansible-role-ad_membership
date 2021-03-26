# Ansible role Active Directory membership

Manage membership of managed host with Active Directory.

- [Ansible role AD membership](#ansible-role-ad-membership)
  - [License](#license)
  - [Author Information](#author-information)
  - [Requirements](#requirements)
  - [Dependencies](#dependencies)
  - [Compatibility](#compatibility)
  - [Role Variables](#role-variables)
  - [Example Playbook](#example-playbook)
  - [Useful shell commands](#useful-shell-commands)
  - [Additional documentation resources](#additional-documentation-resources)

## License

MIT / BSD

## Author Information

- Maintained by [Michael Cleary](https://www.linkedin.cm/in/cleary.michaelj)
- Originally made by: [Kasra Amirsarvari](https://www.linkedin.com/in/caseraw)


## Requirements

- Ensure a package manager is available and configured with the correct package sources and repositories.
- Ensure privileged permissions are set for the user executing this role to:
  - Install and uninstall.
  - Edit files such as, `/etc/krb5.conf` and `/etc/sssd/sssd.conf`.
  - Manage `systemd` services for `realmd`, `sssd` and `addjobd`.
- Ensure network traffic to the Windows Domain Controller.

## Dependencies

N/A

## Compatibility

Compatible with the following list of operating systems:

- CentOS 7
- CentOS 8
- RHEL 7.x
- RHEL 8.x
- Oracle Linux 7.x
- Oracle Linux 8.x

## Role Variables

| Variable name | Description |
|---------------|-------------|
| ad_membership_sa_username | Service account username for the AD (encrypted). |
| ad_membership_sa_password | Service account password for the AD (encrypted). |
| ad_membership_required_packages | List of required packages to install. |
| ad_membership_use_discovered_domain_controller | Whether to use the discovered AD controller based on domain. |
| ad_membership_ad_controller_random_selection | Whther to randomly select an AD controller or just the first in the list. |
| ad_membership_ad_controllers | A list of AD controllers. |
| ad_membership_computer_ou | The AD Organizational Unit to place the Computer object in. |
| ad_membership_ou_user_search_base | The AD Organizational Unit to search users in. |
| ad_membership_netbios_max_length | Maximum character length for Netbios hostname check. |
| ad_membership_leave_ad | Whether to leave the AD and remove the Computer object from the OU. |
| ad_membership_allowed_group_list | Combined list of other lists that start with the name `ad_membership_allowed_group_list_`. |
| ad_membership_allowed_group_list_default | A default list of groups to allow. |
| ad_membership_molecule_dummy | Dummy switch to bypass entire converge playbook. |

## Example Playbook

```yaml
---
- name: Manage membership of managed host with Active Directory
  become: True
  gather_facts: True
  vars_files:
    - /path/to/vault/file.yml
  tasks:
    - import_role:
        name: ad_membership
      vars:
        ad_membership_required_packages:
          - krb5-workstation
          - krb5-libs
          - adcli
          - realmd
          - authconfig
          - samba-client
          - samba-common
          - samba-common-tools
          - sssd
          - sssd-ad
          - sssd-krb5
          - oddjob
          - oddjob-mkhomedir
        ad_membership_use_global_domain_controller: False
        ad_membership_ad_controller_random_selection: False
        ad_membership_ad_controllers:
          - ad1.example.com
          - ad2.example.com
        ad_membership_computer_ou: OU=Servers,DC=example,DC=com
        ad_membership_netbios_max_length: 15
        ad_membership_leave_ad: False
        ad_membership_allowed_group_list_default:
          - Special-Group-01
          - super_special_group_01
        ad_membership_allowed_group_list_something:
          - Special-Group-02
          - super_special_group_02
        ad_membership_allowed_group_list_something_else:
          - Special-Group-03
          - super_special_group_03

...
```

## Useful shell commands

Discover AD controller and domain specifics.

```shell
dig -t SRV _ldap._tcp.ad.example.com
dig -t SRV _ldap._tcp.dc._msdcs.ad.example.com
```

## Additional documentation resources

The following links provide more information about **sssd** and it's usage.

- <https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/windows_integration_guide/sssd-integration-intro>
