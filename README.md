
Ansible ExaBGP
==============

Ansible role to deploy and manage [ExaBGP](https://github.com/Exa-Networks/exabgp).

Requirements
------------

No specific pre-requirements (as of this update/fork).

This update switches from using `pip` to an included RPM package installed with `yum` for RedHat and derivatives.

On Ubuntu (Xenial), this role already used `apt`, and may install `pip` if it is missing and if needed.

Role Variables
--------------


- **exabgp\_pid\_file:** Path to PID file.  
  Defaults to `/var/run/exabgp.pid`.

- **exabgp\_user:** User for *ExaBGP* process to run as.  
  Defaults to *nobody*.

- **exabgp\_group:** Group for *ExaBGP* process to run as.  
  Defaults to *nobody*.

- **exabgp\_env:** This is a dictionary of dictionaries that configures how the *ExaBGP* process starts and runs. Refer to *ExaBGP* [documentation](https://github.com/Exa-Networks/exabgp/wiki) for what are valid configuration values.  A typical configuration knob is of the form `exabgp.log.enable`. The first two parts, `exabgp.log` form the section name. The last part is configuration key that accepts a parameter.  
  This defaults to:  

```
exabgp_env:
    exabgp.daemon:
      daemonize: true
      pid: "{{ exabgp_pid_file }}"
    exabgp.log:
      all: false
      enable: true
      destination: syslog
```

- **exabgp\_conf**: This is a list of configuration sections for `exabgp.conf`. Each element in the list is a dictionary with three possible keys: *section* (such as *group*, *neighbor*, etc), *name* (optional), and *config* that defines the actual configuration for the section.  
  This defaults to an empty list. An example is:

```
  exabgp_conf:
    - section: group
      name: EXAMPLE
      config: |-
        local-as 64512;
        local-address 127.0.0.1;
        hold-time 180;
        group-updates;
        family {
          ipv4 unicast;
        }
        neighbor 127.0.0.10 {
          description "EXAMPLE";
          peer-as 64513;
        }
```

Dependencies
------------

No dependency on other roles.

Example Playbook
----------------

```
    - hosts: bgp_servers
      vars:
        exabgp_conf:
          - section: neighbor
            name: 127.0.0.15
            config: |-
              local-as 64512;
              local-address 127.0.0.1;
              description "Example neighbor";
              peer-as 64514;
              hold-time 5;
              static {
                route 127.0.0.2 {
                  next-hop 127.0.0.3;
                }
              }
              family {
                inet unicast;
              }
      roles:
         - { role: sfromm.exabgp }
```

License
-------

GPL v2.0

Authors Information
-------------------

 - Original author, see: [github.com/sfromm](https://github.com/sfromm)
 - Also includes contributions from: [github.com/Foxlik](https://github.com/Foxlik)
 - This fork updated by Daniel @ [AFRINIC.net](https://afrinic.net)
