# Public OpenNebula Marketplace Appliances

This is a repository with YAML metadata of the appliances available in the
official public [OpenNebula Marketplace](https://marketplace.opennebula.systems),
to be used with the [simple Marketplace](https://github.com/OpenNebula/appmarket-simple)
server implementation.

All appliances here are built and certified by the OpenNebula Systems.
**No community appliances are accepted into the public OpenNebula Marketplace
anymore!**.

:exclamation: Important when **updating existing appliance**:

- bump `version`, so that OpenNebula clients updates their cached metadata
- update `creation_time` to current epoch time
- image metadata (size, checksums) must be current

## New appliance (internal steps)

Filename must be a unique UUID with `.yaml` suffix (subdirectory within `appliances/` is not important, but should reflect the nature of appliance). Use `uuidgen` or `uuid` to generate a new one. Following appliance types can be created

- **Image** - Virtual Machine Image with optional Template
- **Service** - Template for OneFlow Service
- **Virtual Machine Template** - Virtual Machine Template with images taken from other Appliances

### Image (with optional VM Template)

Example of an appliance that you can be used as a basis for a new one.

```yaml
---
name: Name of the appliance
version: Version of this specific appliance
publisher: Company name
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

# Informative Image format, options: qcow2, raw, vmdk
format: qcow2

# Time in epoch format and UTC. You can get it executing "date +%s"
creation_time: 1443105849

# Informative OS, version, arch., and hypervisor
os-id: Debian
os-release: '8.4'
os-arch: x86_64
hypervisor: KVM

# Compatibility with OpenNebula releases. Appliance will be
# offered only to OpenNebula clients with matching version!!
opennebula_version: 4.8, 4.10, 4.12, 4.14, 5.0, 5.2, 5.4

# The template for the appliance without disks and in YAML format
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
    md5: a661b9101d0e7427a9495967292f7285
    sha256: 3f84c3e90b488c476d516d40863c69b534079843851b2b3b2476a29484a275a9
```

Skip the `opennebula_template:` for **image-only** appliances.

### Service (since ONE 6.0+)

Example of an appliance that you can be used as a basis for OneFlow Service:

```yaml
---
name: Name of the appliance
version: Version of this specific appliance
publisher: Company name
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

# Informative Image format, options: qcow2, raw, vmdk
format: qcow2

# Time in epoch format and UTC. You can get it executing "date +%s"
creation_time: 1443105849

# Informative OS, version, arch., and hypervisor
os-id: Debian
os-release: '8.4'
os-arch: x86_64
hypervisor: KVM

# Compatibility with OpenNebula releases. Appliance will be
# offered only to OpenNebula clients with matching version!!
# NOTE: OneFlow Marketplace appliances are supported since 6.0!
opennebula_version: 6.0

# Appliance type, **MUST** be set to SERVICE_TEMPLATE
type: 'SERVICE_TEMPLATE'

# Structure with OneFlow roles and Marketplace Appliances used to run them.
# The referred Appliances are going to be exported to client OpenNebulas
# automatically alongside with the export of this OneFlow Appliance.
roles:
  master: 'Service Kubernetes 1.18 - KVM'
  worker: 'Service Kubernetes 1.18 - KVM'

# OneFlow Template, needs to be a JSON encoded in a string!
# Role names must correspond to names listead above.
opennebula_template: '{
  "name": "Kubernetes",
  "deployment": "straight",
  "description: "",
  "roles": [
    {
      "name": "master",
      "cardinality": 1,
      ...
    },
    ...
  ],
  "networks": {
    ...
  },
  "ready_status_gate": true
}'

# Logo to display in the web interface. You can check the available logos in
# "logos" dir in this repository. You can also submit a new logos using pull
# requests
logo: debian.png

# No images are bundled with OneFlow service,
# but the images structure must exist and be empty.
images: []
```

### Virtual Machine Template (since ONE 6.0+)

Example of an appliance, which doesn't come with any disks, but imports other appliances (referenced in `disks`) and uses their images as own:

```yaml
---
name: Name of the appliance
version: Version of this specific appliance
publisher: Company name
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

# Informative Image format, options: qcow2, raw, vmdk
format: qcow2

# Time in epoch format and UTC. You can get it executing "date +%s"
creation_time: 1443105849

# Informative OS, version, arch., and hypervisor
os-id: Debian
os-release: '8.4'
os-arch: x86_64
hypervisor: KVM

# Compatibility with OpenNebula releases. Appliance will be
# offered only to OpenNebula clients with matching version!!
# NOTE: VM Template Marketplace appliances are supported since 6.0!
opennebula_version: 6.0

# Appliance type, **MUST** be set to VMTEMPLATE
type: 'VMTEMPLATE'

# List of other Marketplace Appliances, which will be imported alongside
# with this appliance, and their images used as disks of current appliance.
disks:
  - 'Ubuntu 20.04'
  - 'Empty disk'

# The template for the appliance without disks and in YAML format
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

# No images are bundled with this appliance type,
# but the images structure must exist and be empty.
images: []
```
