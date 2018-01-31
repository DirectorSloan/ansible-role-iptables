# ansible-role-iptables
iptables role to install and configure iptables on Linux

Ansible Role: iptables
=====================

An Ansible role that installs [iptables][] and configures it. It is basically written for RHEL/CentOS but can be ported to other distributions. 

Table of Contents
-----------------

<!-- toc -->

- [Description](#description)
- [Requirements](#requirements)
- [Role Variables](#role-variables)
  * [Basic Variables](#basic-variables)
- [Dependencies](#dependencies)
- [Example Playbook](#example-playbook)
  * [Top-Level Playbook](#top-level-playbook)
  * [Role Dependency](#role-dependency)
- [License](#license)
- [Author Information](#author-information)

<!-- tocstop -->

Description
-----------

This role will remove firewalld and install iptables and ensure it is enabled and started. The configuration is based on a restrictive firewall design which blocks most traffic by default. Be carefull when using it the first time! With the default Jinja2 Template only the Chains INPUT FORWARD and OUTPUT are used in the filter table. If the docker boolean may be used there will be also a DOCKER Chain in the tables filter and nat.

Requirements
------------

- Ansible 2+

Role Variables
--------------

### Basic Variables

Variables with defaults:

```yml
iptables_public_interface: eth0
iptables_template: 'iptables.j2'
iptables_tcp_rules:
  - destinationport: 22
    sourceaddress: '0.0.0.0/0'
    comment: 'SSH from everywhere'

iptables_udp_rules:
  - destinationport: 123
    sourceaddress: '0.0.0.0/0'
    comment: 'NTP from everywhere'

```

These are empty by default, you can set a allow all interconnect interface based on 10.10.20.0/24 network by defining a interface name with `iptables_interconnect_interface` and if you have docker on your host you will need some extra rules which can be enabled using the `host_use_docker` variable:

```yml
iptables_interconnect_interface: 'eth1'
iptables_interconnect_range: '10.10.10.0/24'
host_use_docker: 'true'
```

### TCP Rules

Every exception in tcp can be added by listing each rule like this:

```yml
iptables_tcp_rules:
  - destinationport: 80
    sourceaddress: '1.2.3.0/24'
    comment: 'HTTP from 1.2.3.0 network'

  - destinationport: 443
    sourceaddress: '1.2.3.0/24'
    comment: 'HTTPS from 1.2.3.0 network'

  - destinationport: 5666
    sourceaddress: '1.2.3.4'
    comment: 'NRPE communication from nagios server'


```

### UDP Rules

Same for udp:

```yml
iptables_udp_rules:
  - destinationport: 53
    sourceaddress: '1.2.3.0/24'
    comment: 'DNS from 1.2.3.0 network'

  - destinationport: 67
    sourceaddress: '1.2.3.4'
    comment: 'DHCP client from 1.2.3.4 host'

```

Dependencies
------------

None.

Example Playbook
----------------

Add to `requirements.yml`:

```yml
---

- src: sloan87.iptables

...
```

Download:

```console
$ ansible-galaxy install -r requirements.yml
```

### Top-Level Playbook

Write a top-level playbook:

```yml
---

- name: worker server
  hosts: worker

  roles:
    - role: sloan87.iptables
      tags:
        - firewall
        - iptables
        - network
        - security

...
```

### Role Dependency

Define the role dependency in `meta/main.yml`:

```yml
---

dependencies:

  - role: sloan87.iptables
    tags:
      - firewall
      - iptables
      - network
      - security

...
```

License
-------

MIT

Author Information
------------------

This role was created in 2017 by Ben Langenberg [sloan87 at GitHub][sloan87], HPC cluster systems administrator at the [Helmholtz-Centre for Environmental Research GmbH - UFZ][ufz], role skel based on a draft by Christian Krause aka [wookietreiber at GitHub][wookietreiber].


[ufz]: https://www.ufz.de
[sloan87]: https://github.com/sloan87
[wookietreiber]: https://github.com/wookietreiber
[iptables]: http://www.netfilter.org/projects/iptables/index.html
