# Testing Ansible Roles with Molecule
Date: 21 Apr 2023

This is a collection of notes on trying to use [Ansible Molecule](https://molecule.readthedocs.io/) to test the Ansible Roles.

Since some of the roles do interact with the systemd, they should be
tested in the containers that can run systemd. Starting from the
version xxxx of systemd Docker based containers cannot have systemd as
a process with PID 1 (something to do with the shared cgroup
namespaces). Podman does not have this issue, and can work with the
same docker images as Docker. This post is an attempt to document
actions taken on the Arch Linux running systemd version 253.3-3, and
kernel 6.2.10-arch1-1

## Installing and configuring podman
 - Install podman with `pacman -S podman`
 - Make sure entries in the `/etc/subuid` and `/etc/subgid` for the
   user that will be running the container exist. According to the
   [Arch Wiki Article](https://wiki.archlinux.org/title/Podman) the
   minimum UID and GID for containers should be at least 524288.
   The format of the files are `username:start:count`, so the
   following entries should be added to both files:
```
username:524288:65535
```

## Installing and configuring Molecule
Create new virtual environment, and install molecule and podman driver:
```
# virtualenv --prefix Molecule molecule
# source molecule/bin/activate
# pip3 install molecule 'molecule-plugins[podman]'
```

## Adding molecule to the existing role
In the role's directory, run `molecule init scenario -r <rolename> -d podman`

This will create the following structure in the `molecule` directory:
```
molecule/
└── default
    ├── converge.yml
    ├── molecule.yml
    └── verify.yml

2 directories, 3 files
```

## Configuring Debian instance
Instances against which roles are tested are defined in the
`molecule/default/molecule.yml` file, in the `platforms` section.
By default Molecule provides a single instance called "instance" based
on CentOS. 
```
platforms:
  - name: instance
    image: quay.io/centos/centos:stream8
    pre_build_image: true
```

[jrei/systemd-debian](https://hub.docker.com/r/jrei/systemd-debian)
image can be used to create Debian-based images with `systemd` running
in them, by making sure `platforms` looks like this:
```
platforms:
  - name: bullseye
    image: docker.io/jrei/systemd-debian:bullseye
    tmpfs:
      - /run
      - /tmp
    volumes:
      - /sys/fs/cgroup:/sys/fs/cgroup:ro
    capabilities:
      - SYS_ADMIN
    command: "/lib/systemd/systemd"
    pre_build_image: true
```
## Resources
The two blog posts below describe how to use Podman instead of Docker.
 - [Developing and Testing Ansible Roles with Molecule and Podman - Part 1](https://www.ansible.com/blog/developing-and-testing-ansible-roles-with-molecule-and-podman-part-1)
 - [Developing and Testing Ansible Roles with Molecule and Podman - Part 2](https://www.ansible.com/blog/developing-and-testing-ansible-roles-with-molecule-and-podman-part-2)

<!--  LocalWords:  Ansible
 -->
