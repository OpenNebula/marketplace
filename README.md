# Marketplace Appliances

Each Marketplace Appliance consists of a YAML file that contains the metadata
and an image file stored in a web server.

The steps to submit a new appliance or make changes to one of the available
images is as follows:

* Clone this repository
* Make changes and upload them to your repo
* Create a pull request

**Note**: When creating new YAML appliances put them in a directory with
the name of the publisher and try not to use spaces in its dir name
("My Company" -> "My\_Company").

For **filename choose new random UUID** with `.yaml` suffix. You can use
`uuidgen` or `uuid` commands to generate a new one.

Here is an example of an appliance that you can use as a basis for a new one.


```yaml
---
name: Name of the appliance
version: Version of this specific appliance
publisher: Your name or company
description: |-
  Description of this appliance. The format is *Markdown* so you'll be able
  to [link](https://guides.github.com/features/mastering-markdown/) or
  add bullet lists:

  * one
  * two
  * I am **bold**
short_description: This is a short description that appears in apps list

# Assorted list of tags, you can add version of OpenNebula where it was
# tested and things like OS or application
tags:
- debian
- router
- dhcp server
- '4.8'
- '4.10'
- '4.12'
- '4.14'
- '5.0'

# Other formats could be raw or vmdk
format: qcow2

# Time in epoch format and UTC. You can get it executing "date +%s"
creation_time: 1443105849
os-id: Debian
os-release: '8.4'
os-arch: x86_64
hypervisor: KVM

# This is a string and only visible in the web interface
opennebula_version: 4.8, 4.10, 4.12, 4.14, 5.0

# The template for the appliance without disks and in yaml format
opennebula_template:
  context:
    network: 'YES'
    ssh_public_key: "$USER[SSH_PUBLIC_KEY]"
  cpu: '1'
  graphics:
    listen: 0.0.0.0
    type: vnc
  memory: '768'
  os:
    arch: x86_64
  logo: images/logos/debian.png

# Logo to display in the web interface. You can check the available logos in
# "logos" dir in this repository. You can also submit a new logos using pull
# requests
logo: debian.png

# Array with all images related to this app
images:
- name: Debian-8.4
  url: https://s3-eu-west-1.amazonaws.com/opennebula-marketplace/debian8-5.0.1-fix.qcow2c
  type: OS
  dev_prefix: vd
  driver: qcow2
  # Size in bytes of the image, not the file. You can use `qemu-img info` command
  # to retrieve the virtual size.
  size: 2147483648
  checksum:
    # You can also specify "sha1" hash
    md5: a661b9101d0e7427a9495967292f7285
```



