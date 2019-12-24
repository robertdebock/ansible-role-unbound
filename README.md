unbound
=========

<img src="https://docs.ansible.com/ansible-tower/3.2.4/html_ja/installandreference/_static/images/logo_invert.png" width="10%" height="10%" alt="Ansible logo" align="right"/>
<a href="https://travis-ci.org/robertdebock/ansible-role-unbound"> <img src="https://travis-ci.org/robertdebock/ansible-role-unbound.svg?branch=master" alt="Build status"/></a> <img src="https://img.shields.io/ansible/role/d/45335"/> <img src="https://img.shields.io/ansible/quality/45335"/>

<a href="https://github.com/robertdebock/ansible-role-unbound/actions"><img src="https://github.com/robertdebock/ansible-role-unbound/workflows/GitHub%20Action/badge.svg"/></a>

Install and configure unbound on your system.

Example Playbook
----------------

This example is taken from `molecule/resources/playbook.yml` and is tested on each push, pull request and release.
```yaml
---
- name: Converge
  hosts: all
  become: yes
  gather_facts: yes

  roles:
    - role: robertdebock.unbound
```

The machine you are running this on, may need to be prepared, I use this playbook to ensure everything is in place to let the role work.
```yaml
---
- name: Converge
  hosts: all
  become: yes
  gather_facts: no

  roles:
    - role: robertdebock.bootstrap
    - role: robertdebock.buildtools
    - role: robertdebock.core_dependencies
```

After running this role, this playbook runs to verify that everything works, this may be a good example how you can use this role.
```yaml
---
- name: Verify
  hosts: all
  become: yes
  gather_facts: yes

  vars:
    _nslookup_package:
      default:
        - hostname
      Amazon-2018:
        - net-tools
      Alpine:
        - net-tools
      Archlinux:
        - net-tools
      CentOS:
        - dbus

    nslookup_package: "{{ _nslookup_package[ansible_distribution ~ '-' ~ ansible_distribution_major_version] | default(_nslookup_package[ansible_distribution] | default(_nslookup_package['default'])) }}"

  tasks:
    - name: install nslookup
      package:
        name: "{{ nslookup_package }}"
        state: present

    - name: nslookup robertdebock.com
      command: nslookup robertdebock.com
```

Also see a [full explanation and example](https://robertdebock.nl/how-to-use-these-roles.html) on how to use these roles.

Role Variables
--------------

These variables are set in `defaults/main.yml`:
```yaml
---
# defaults file for unbound

unbound_temporary_directory: /tmp
```

Requirements
------------

- Access to a repository containing packages, likely on the internet.
- A recent version of Ansible. (Tests run on the current, previous and next release of Ansible.)

The following roles can be installed to ensure all requirements are met, using `ansible-galaxy install -r requirements.yml`:

```yaml
---
- robertdebock.bootstrap
- robertdebock.buildtools
- robertdebock.core_dependencies
- robertdebock.service

```

Context
-------

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://robertdebock.nl/) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/robertdebock/drawings/artifacts/unbound.png "Dependency")


Compatibility
-------------

This role has been tested on these [container images](https://hub.docker.com/):

|container|tags|
|---------|----|
|amazon|Candidate, 2018.03|
|debian|all|
|el|7, 8|
|fedora|all|
|opensuse|all|
|ubuntu|artful, bionic|

The minimum version of Ansible required is 2.8 but tests have been done to:

- The previous version, on version lower.
- The current version.
- The development version.

Exceptions
----------

Some variarations of the build matrix do not work. These are the variations and reasons why the build won't work:

| variation                 | reason                 |
|---------------------------|------------------------|
| alpine | Idempotence test failed: start and enable unbound |


Testing
-------

[Unit tests](https://travis-ci.org/robertdebock/ansible-role-unbound) are done on every commit, pull request, release and periodically.

If you find issues, please register them in [GitHub](https://github.com/robertdebock/ansible-role-unbound/issues)

Testing is done using [Tox](https://tox.readthedocs.io/en/latest/) and [Molecule](https://github.com/ansible/molecule):

[Tox](https://tox.readthedocs.io/en/latest/) tests multiple ansible versions.
[Molecule](https://github.com/ansible/molecule) tests multiple distributions.

To test using the defaults (any installed ansible version, namespace: `robertdebock`, image: `fedora`, tag: `latest`):

```
molecule test

# Or select a specific image:
image=ubuntu molecule test
# Or select a specific image and a specific tag:
image="debian" tag="stable" tox
```

Or you can test multiple versions of Ansible, and select images:
Tox allows multiple versions of Ansible to be tested. To run the default (namespace: `robertdebock`, image: `fedora`, tag: `latest`) tests:

```
tox

# To run CentOS (namespace: `robertdebock`, tag: `latest`)
image="centos" tox
# Or customize more:
image="debian" tag="stable" tox
```

License
-------

Apache-2.0


Author Information
------------------

[Robert de Bock](https://robertdebock.nl/)
