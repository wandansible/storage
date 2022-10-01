Ansible role: Storage
=====================

Configure storage.
This role can configure and mount filesystems, enable and
configure NFS mounts, create and enable a swap file and create
a local scratch directory.

Requirements
------------

To use this role, the python package `jmespath` must be installed on the host running ansible.

Role Variables
--------------

```
ENTRY POINT: main - Configure storage

        This role can configure and mount filesystems, enable and
        configure NFS mounts, create and enable a swap file and create
        a local scratch directory.

OPTIONS (= is mandatory):

- idmapd_domain
        The NFSv4 domain name the host belongs to
        [Default: (null)]
        type: str

- scratch_backend_location
        Local filesystem directory to create to store a local scratch
        filesystem on. If unset a scratch directory will not be
        created.
        [Default: (null)]
        type: str

- scratch_dir
        Path to create a symlink from scratch_backend_location to if
        scratch_backend_location is defined and different to
        scratch_dir
        [Default: /scratch]
        type: str

- storage_mounts
        List of filesystem mounts
        [Default: (null)]
        elements: dict
        type: list

        OPTIONS:

        = fstype
            Filesystem type

            type: str

        - opts
            Comma-separated list of mount options
            [Default: (null)]
            type: str

        = path
            Path to the mount point

            type: str

        = src
            Device, or NFS volume, or label (e.g. UUID=) to be mounted
            on path

            type: str

- swap_file
        Path for the swap file
        [Default: /swapfile]
        type: str

- swap_file_size
        Size of the swap file, given as a number of data units, e.g.
        "4GB". If this is set to zero size, e.g. 0GB, then the swap
        file is not created.
        [Default: 0GB]
        type: str
```

Installation
------------

This role can either be installed manually with the ansible-galaxy CLI tool:

    ansible-galaxy install git+https://github.com/wandansible/storage,main,wandansible.storage
     
Or, by adding the following to `requirements.yml`:

    - name: wandansible.storage
      src: https://github.com/wandansible/storage

Roles listed in `requirements.yml` can be installed with the following ansible-galaxy command:

    ansible-galaxy install -r requirements.yml

Example Playbook
----------------

    - hosts: all
      roles:
         - role: wandansible.storage
           become: true
           vars:
             scratch_backend_location: "/scratch"

             idmapd_domain: "example.org"
             storage_mounts:
               - src: /dev/sda1
                 path: /scratch
                 fstype: ext4
                 opts: errors=remount-ro

               - src: nfs.example.org:/home
                 path: /home
                 fstype: nfs4
                 opts: rsize=8192,wsize=8192,timeo=300,retrans=20,hard,bg
