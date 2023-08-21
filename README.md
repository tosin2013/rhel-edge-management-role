RHEL EDGE Management
=========

The following role builds images for fleet manager. 

[![ansible-lint](https://github.com/tosin2013/rhel-edge-management-role/actions/workflows/ansible-lint.yml/badge.svg)](https://github.com/tosin2013/rhel-edge-management-role/actions/workflows/ansible-lint.yml)

Requirements
------------
* Ansible
* A Red hat user account
  * https://console.redhat.com/ 
* Get offline token
> [Red Hat API Tokens](https://access.redhat.com/management/api)
* An SSH Public Key 
```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa
```
* install ansible collections
```
ansible-galaxy collection install -r collections/requirements.yml
```
* install ansible roles
```
ansible-galaxy role install -r roles/requirements.yml
```

To Develop Using devspaces see link below
-------------
* [Devspaces Install](devspaces.md)

Role Variables
--------------

Type  | Description  | Default Value
--|---|--
rh_offline_authentication_api_bearer_token | offline Token for API Acccess |rh_api_offline_token 
iso_download_directory | Default iso download directory | "/tmp/generated_iso"
workspace | Default workspace for fleet manager | "/tmp/workspace"
remove_workspace | Default workspace directory | true
rhsm_username| RHEL username | username 
rhsm_password| RHEL Password |password 
ssh_pub_key| Public SSH key | "ssh-rsa XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
device_group_name | Name of device Group |"my-device-name-group"
image_name | Name of image  |"test-image"
image_type | Type of image |" image-installer"  # guest-image=qcow2 image-installer=iso
username | Name of user of target edge device |"admin"
distribution | RHEL Build distribution |"rhel-86"
description | Descrition of image |"sample description"
packages | example package list to be installed on image |"curl net-tools podman tar bind-utils git"
arch |RHEL architecture for taget enviornments  |"x86_64"
rhc_org_id | RHEL ORG ID used to register devices |"your_rhc_org_id"
rhc_activation_key | RHEL activation Key used to register devices |"your_rhc_activation_key"
os_variant "rhel8.6"
enable_kickstart| Add custom kickstart to deployment | true
default_kickstart_url| Default kickstart url  |https://raw.githubusercontent.com/red-hat-se-rto/rhel-fleet-management/main/inventories/lab/applications/quarkuscoffeeshop-majestic-monolith-fleet-manger/fleet.kspost"


Dependencies
------------

* Ansible 

Example Playbook
----------------
```
vim myplaybook.yml
- hosts: localhost
  remote_user: root
  roles:
    - rhel-edge-management-role
    - rhel-egde-on-vmware
```

Example Vars
------------
This file works with RPM-DNF, you can manage the system software by using the DNF package manager and updated RPM packages. This is a simple and adaptive method of managing and modifying the system over its lifecycle.
```
vim vars.yml
---
rh_offline_authentication_api_bearer_token: "XXXXXXXXXXXXXXXXXXXXXXXXXXXXc"
rhsm_username:  login@example.com
rhsm_password:  yourpassword 

#########################################################
## Directories
### For Tower 
## for workstation: iso_download_directory: "/opt/generated_iso"
iso_download_directory: "/tmp/generated_iso"
image_type: image-installer   # guest-image=qcow2 image-installer=iso

### For Tower 
## for workstation: workspace: "/opt/lib/aws/projects/workspace"
workspace: "/tmp"

#########################################################
## Required Files
ssh_pub_key: "CHANGEME"

#########################################################
## required variables
create_device_name_group: true
device_group_name: "my-device-name-group"
create_image: true

#########################################################
## image atrributes
image_name: "test-image" # Example: "test-image-11-13-2022-b1"
username: "admin"
distribution: "rhel-92"
description: "sample description"
packages:  ["curl", "net-tools", "podman", "tar", "bind-utils", "git"]
# for osbuild tree  deployments image-build for microshift use image-microshift-build
# for rpm image builder use image-builder
build_template: image-builder
arch: "x86_64"
enable_kickstart: false 
kickstart_path: "https://raw.githubusercontent.com/Red-Hat-SE-RTO/rhel-fleet-management/main/inventories/lab/applications/quarkuscoffeeshop-majestic-monolith-fleet-manger/fleet.kspost"

#########################################################
## automated management variables
## https://access.redhat.com/management/activation_keys
rhc_org_id: "1111111"
rhc_activation_key: "CHANGEME"

#########################################################
## optional variables
## osinfo-query os
os_variant: "rhel8.6"

#########################################################
## Dont need to change 
compiled_uri_headers: {}
```

This file is a work in progress, it will deploy vms on vmware and deploy the iso to the vm.
```
cat >vars_vmware_test.yml<<EOF
---
rh_offline_authentication_api_bearer_token: "XXXXXXXXXXXXXXXXXXXXXXXXXXXXc"
rhsm_username:  login@example.com
rhsm_password:  yourpassword 

ssh_pub_key: "ssh-rsa XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"


create_device_name_group: true
device_group_name: "my-device-name-group"
create_image: true


#########################################################
## image atrributes
image_name: "imagename"
username: "admin"
distribution: "rhel-86"
description: "sample description"
packages: 'curl net-tools podman tar bind-utils git'
arch: "x86_64"

rhc_org_id: "your_rhc_org_id"
rhc_activation_key: "your_rhc_activation_key"


#########################################################
## image atrributes
## Vmware Settings
## https://github.com/Red-Hat-SE-RTO/rhel-egde-on-vmware/blob/main/defaults/main.yml
vcenter_hostname: "vsphere.example.com"
vcenter_username: "administrator@vsphere.local"
vcenter_password: "P@$$w0rD"
vcenter_datacenter: Datacenter1
vcenter_cluster: "Cluster"
vcenter_datastore: datastore
vmware_folder: 'edge-deployments'
vcenter_network: "VM Network"
vmware_hostname: "{{ image_name }}"
iso_path_loc: "ISOs/{{ image_name }}.iso"
iso_src: "/tmp/generated_iso/{{ image_name }}.iso"
EOF
```

How-To 
--------

To Deploy 

```
ansible-playbook -i inventory myplaybook.yml --extra-vars "@vars.yml"
```

Create device group on the redhat console website
> https://console.redhat.com/edge/fleet-management
```
 ansible-playbook -i inventory myplaybook.yml --extra-vars "@vars.yml" -t create_device_group
```

Create and build rhel image on redhat console using RPM-DNF.
 > https://console.redhat.com/edge/manage-images
```
 ansible-playbook -i inventory myplaybook.yml --extra-vars "@vars.yml" -t rpm_dnf_build_image
```

Delete a rhel image on redhat console using RPM-DNF.
 > https://console.redhat.com/edge/manage-images
```
 ansible-playbook -i inventory myplaybook.yml --extra-vars "@vars.yml" -t delete_dnf_build_image
```

Create and build rhel image on redhat console using ostree.
 > https://console.redhat.com/edge/manage-images
```
 ansible-playbook -i inventory myplaybook.yml --extra-vars "@vars.yml" -t build_image
```


Wait for ostree build to complete
 > https://console.redhat.com/edge/manage-images
```
 ansible-playbook -i inventory myplaybook.yml --extra-vars "@vars.yml" -t get_build_status
```

Wait for RPM-DNF image build to complete
 > https://console.redhat.com/insights/image-builder
```
 ansible-playbook -i inventory myplaybook.yml --extra-vars "@vars.yml" -t get_dnf_build_status
```

Download ISO from redhat console
 > https://console.redhat.com/edge/manage-images
```
 ansible-playbook -i inventory myplaybook.yml --extra-vars "@vars.yml" -t download_latest_iso
```

Auto register vms so they will populate on 
 > https://console.redhat.com/insights/inventory/
```
 ansible-playbook -i inventory myplaybook.yml --extra-vars "@vars.yml" -t configure_auto_registration
```

Deploy ISOs
----------
Start VM on vmware
> for custom isos user the append _fleet_out.iso to  `iso_path_loc` and `iso_src`
```
$ ansible-playbook -i inventory myplaybook.yml --extra-vars "@vars.yml" -t vmware_create_folder,vmware_check_for_iso,vmware_upload_iso  -vv
 
$ ansible-playbook -i inventory myplaybook.yml --extra-vars "@vars.yml" -t vmware_deploy_vms -vv
```


## Currently WIP
* Update images
* custom kickstart integration

API Documentation
-------
https://console.redhat.com/docs/api/edge

Links
-------
* https://github.com/tosin2013/rhel-edge-management
* [Create RHEL for Edge images and configure automated management](https://access.redhat.com/documentation/en-us/edge_management/2022/html-single/create_rhel_for_edge_images_and_configure_automated_management/index#doc-wrapper)
* [Working with systems in the edge management application](https://access.redhat.com/documentation/en-us/edge_management/2022/html-single/working_with_systems_in_the_edge_management_application/index)


License
-------

BSD

Author Information
------------------

Tosin Akinosho

