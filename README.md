satellite_create_host
=========

A very simple role to create a host in a Red Hat Satellite. The role uses the Red Hat certified collection
[`redhat.satellite`](https://console.redhat.com/ansible/automation-hub/repo/published/redhat/satellite/). To use the certified collection `redhat.satellite` you need to be
a Red Hat subscriber. If you don't own any subscriptions, you can make use of
[Red Hat's Developer Subscription](https://developers.redhat.com/articles/faqs-no-cost-red-hat-enterprise-linux) which is provided at no cost by Red Hat.

You can also make use of the upstream collection [`theforeman.foreman`](https://docs.ansible.com/ansible/latest/collections/theforeman/foreman/index.html), but you'd need to
change the module name from `redhat.satellite.host` to `theforeman.foreman.host` - but I have *not* tested this.

The role passes all currently (as of 23.12.2023) supported arguments to the module
[`redhat.satellite.host`](https://console.redhat.com/ansible/automation-hub/repo/published/redhat/satellite/content/module/host/).


Role Variables
--------------
| variable                                     | default                      | required | description                                                                    |
| :---------------------------------           | :--------------------------- | :------- | :----------------------------------------------------------------------------- |
| `sat_username`                               | unset                        | true     | username to connect to the Satellite API with                                  |
| `sat_password`                               | unset                        | true     | password to use for the user to connect to the Satellite API with              |
| `sat_server_url`                             | unset                        | true     | Satellite server URL (including http:// or https://)                           |
| `sat_hosts`                                  | unset                        | true     | hosts to create in Satellite                                                   |
| `sat_validate_certs`                         | `true`                       | false    | whether to validate certificates when connecting to the Satellite API          |
| `sat_quiet_assert`                           | `false`                      | false    | whether to quiet assert statements                                             |

Example Playbook
----------------

```
---
- name: 'Create Hosts in a Satellite server'
  hosts: 'satellite.office.int.scheib.me'
  gather_facts: false
  roles:
    - role: 'satellite_create_host'
  vars:
    sat_server_url: 'satellite.example.com'
    sat_username: 'steffen'
    sat_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          [..]
    sat_hosts:
      - name: 'host.example.com'
        architecture: 'x86_64'
        build: true
        comment: 'Sample comment'
        lifecycle_environment: 'lce-default-dev'
        location: 'loc-core/loc-dev'
        content_source: "{{ sat_server_url | ansible.builtin.urlsplit('hostname') }}"
        content_view: 'ccv-default-rhel-8'
        hostgroup: 'hg-core/hg-rhel-8/hg-default-rhel-8/hg-default-rhel-8-dev'
        enabled: true
        operatingsystem: 'RedHat 8.9'
        provision_method: 'build'
        managed: true
        ptable: 'pt-standard_bios'
        pxe_loader: 'PXELinux BIOS'
        subnet: 'sn-172_31_10_0'
        openscap_proxy: "{{ sat_server_url | ansible.builtin.urlsplit('hostname') }}"
        organization: 'org-core'
        owner: '{{ sat_username }}'
        state: 'present'
        interfaces_attributes:
          - name: 'host'
            domain: 'example.com'
            execution: true
            identifier: 'enp6s18'
            ip: '172.31.10.2'
            mac: 'AA:BB:CC:DD:EE:FF'
            managed: true
            primary: true
            provision: true
            subnet: 'sn-172_31_10_0'
            type: 'interface'

      - name: 'host2.example.com'
        architecture: 'x86_64'
        build: true
        comment: 'Sample comment'
        domain: 'example.com'
        lifecycle_environment: 'lce-default-prod'
        location: 'loc-core/loc-prod'
        content_source: "{{ sat_server_url | ansible.builtin.urlsplit('hostname') }}"
        content_view: 'ccv-default-rhel-8'
        hostgroup: 'hg-core/hg-rhel-8/hg-default-rhel-8/hg-default-rhel-8-prod'
        enabled: true
        operatingsystem: 'RedHat 8.9'
        provision_method: 'build'
        managed: true
        ptable: 'pt-standard_bios'
        pxe_loader: 'PXELinux BIOS'
        subnet: 'sn-172_31_10_0'
        openscap_proxy: "{{ sat_server_url | ansible.builtin.urlsplit('hostname') }}"
        organization: 'org-core'
        owner: '{{ sat_username }}'
        state: 'present'
        domain: 'example.com'
        ip: '172.31.10.3'
        mac: 'AA:BB:CC:DD:EE:AA'
...
```

License
-------

GPL-2.0-or-later
