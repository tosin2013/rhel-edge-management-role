RHEL EDGE Management
=========

The following role builds images for fleet manager. 

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

Role Variables
--------------

Type  | Description  | Default Value
--|---|--
rh_offline_authentication_api_bearer_token | offline Token for API Acccess |rh_api_offline_token 
rh_authentication_basic_username| RHEL username | username 
rh_authentication_basic_password| RHEL Password |password 
ssh_pub_key| Public SSH key |"$HOME/.ssh/id_rsa.pub"
device_group_name | Name of device Group |"my-device-name-group"
image_name | Name of image  |"test-image"
username | Name of user of target edge device |"admin"
distribution | RHEL Build distribution |"rhel-86"
description | Descrition of image |"sample description"
packages | package list tio be installed on image |"curl net-tools podman tar bind-utils git"
arch |RHEL architecture for taget enviornments  |"x86_64"
rhc_org_id | RHEL ORG ID used to register devices |"your_rhc_org_id"
rhc_activation_key | RHEL activation Key used to register devices |"your_rhc_activation_key"
os_variant | OS variant target for KVM testing |"rhel8.6"

Dependencies
------------

* Ansible 

Example Playbook
----------------
---
- hosts: localhost
  remote_user: root
  roles:
    - rhel-edge-mangement-role

How-To 
--------

To Deploy 

```
ansible-playbook -i inventory myplaybook.yml 
```

Create device group on the redhat console website
> https://console.redhat.com/edge/fleet-management
```
 ansible-playbook -i inventory myplaybook.yml  -t create_device_group
```


Create and build rhel image on redhat console.
 > https://console.redhat.com/edge/manage-images
```
 ansible-playbook -i inventory myplaybook.yml  -t build_image
```


Wait for build to complete
 > https://console.redhat.com/edge/manage-images
```
 ansible-playbook -i inventory myplaybook.yml  -t get_build_status
```

Download ISO from redhat console
 > https://console.redhat.com/edge/manage-images
```
 ansible-playbook -i inventory myplaybook.yml  -t build_image
```
## Currently WIP
* Update images
* - Auto register vms so they will populate on https://console.redhat.com/insights/inventory/
* custom kickstart integration


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

