Ansible Role: ExaBGP
====================

Ansible role to deploy and manage [ExaBGP](https://github.com/Exa-Networks/exabgp).

Uses pip to install the last version of ExaBGP.

Role default variables
----------------------

```yaml
# If specified, exabgp_version will force to install a specific pip version of
# exabgp instead of the last one.
exabgp_version: null

# Pip package name to install.
exabgp_pip_name: exabgp

# Options to specify in /etc/exabgp/exabgp.env. Dictionary, where first level
# keys are sections, themselves being a dictionary describing the options.
exabgp_env:
  exabgp.daemon:
    daemonize: true
    pid: "{{ exabgp_pid_file }}"
  exabgp.log:
    all: false
    enable: true

# List of configuration sections in exabgp.conf. Each element is a dictionary
# containing three possible keys: section (such as group, neighbor, etc), name
# (optional), and config that defines the actual configuration for the section.
exabgp_conf: []

# Scripts to copy in /etc/exabgp, to be used by the process sections.
exabgp_scripts: {}
```


Example Playbook
----------------

```yaml
- hosts: all
  vars:
    exabgp_env:
      exabgp.daemon:
        daemonize: true
        pid: "{{ exabgp_pid_file }}"
      exabgp.log:
        all: false
        enable: true
    exabgp_scripts:
      watchdog.sh: |-
        #!/bin/bash
        echo "announce watchdog foo"

    exabgp_conf:
      - section: process
        name: watchdog
        config: |-
          run /etc/exabgp/foo.sh;
          encoder json;
      - section: neighbor
        name: EXAMPLE
        config: |-
          description "EXAMPLE";
          local-as 65000;
          local-address 127.0.0.1;
          hold-time 180;
          static {
            route 192.0.2.1/32 next-hop self watchdog foo;
          }


  roles:
    - role: Anthony25.exabgp
```

Dependencies
------------

None

License
-------

GPL v2.0

Authors Information
-------------------

 - Original author, see: [github.com/sfromm](https://github.com/sfromm)
 - Also includes contributions from: [github.com/Foxlik](https://github.com/Foxlik)
 - This fork updated by Daniel @ [AFRINIC.net](https://afrinic.net)
