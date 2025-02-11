# IMT-PracticalAnsible
Practical for ansible 

## Introduction
### Provision vagrant vm

I provisionned vagrant vm with libvirt.

`$ vagrant up --provider=libvirt`

I check that the vm is running.

```
$ vagrant status
[fog][WARNING] Unrecognized arguments: libvirt_ip_command
[fog][WARNING] Unrecognized arguments: libvirt_ip_command
[fog][WARNING] Unrecognized arguments: libvirt_ip_command
[fog][WARNING] Unrecognized arguments: libvirt_ip_command
Current machine states:

rocky                     running (libvirt)
debian                    running (libvirt)
suse                      running (libvirt)
ubuntu                    running (libvirt)

This environment represents multiple VMs. The VMs are all listed
above with their current state. For more information about a specific
VM, run `vagrant status NAME`.
````

We check that we can connect via SSH to the vm.
```
$ vagrant ssh rocky
[fog][WARNING] Unrecognized arguments: libvirt_ip_command
[vagrant@rocky ~]$ 
```

### Install ansible with venv

On debian vm, I installed ansible with venv.
```bash
$ sudo apt update && sudo apt install python3-venv
$ python3 -m venv ansible
$ source ansible/bin/activate
(ansible) $ pip install ansible
```

I check that ansible is installed.
```bash
(ansible) $ ansible --version
ansible [core 2.18.2]
  config file = None
  configured module search path = ['/home/vagrant/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /home/vagrant/ansible/lib/python3.11/site-packages/ansible
  ansible collection location = /home/vagrant/.ansible/collections:/usr/share/ansible/collections
  executable location = /home/vagrant/ansible/bin/ansible
  python version = 3.11.2 (main, Nov 30 2024, 21:22:50) [GCC 12.2.0] (/home/vagrant/ansible/bin/python3)
  jinja version = 3.1.5
  libyaml = True
````

## 1. Install ansible on Ubuntu

```$ apt update && apt-cache search ansible
vagrant@ubuntu:~$ apt-cache search ansible
ansible - Configuration management, deployment, and task execution system
ansible-core - Configuration management, deployment, and task execution system
...
```

We have two packages available, `ansible` and `ansible-core`. I will install `ansible` package in order to have the full version of ansible with collections.

```bash
vagrant@ubuntu:~$ sudo apt install ansible
```

I check that ansible is installed.
```bash
vagrant@ubuntu:~$ ansible --version
ansible 2.10.8
  config file = None
  configured module search path = ['/home/vagrant/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 3.10.12 (main, Jun 11 2023, 05:26:28) [GCC 11.4.0]
```

## 2. Install with ppa

```bash
$ sudo apt-add-repository ppa:ansible/ansible
Repository: 'deb https://ppa.launchpadcontent.net/ansible/ansible/ubuntu/ jammy main'
Description:
Ansible is a radically simple IT automation platform that makes your applications and systems easier to deploy. Avoid writing scripts or custom code to deploy and update your applications— automate in a language that approaches plain English, using SSH, with no agents to install on remote systems.
...

$ sudo apt update && sudo apt install ansible

```

I check the version of ansible.
```bash
vagrant@ubuntu:~$ ansible --version
ansible [core 2.17.8]
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/home/vagrant/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  ansible collection location = /home/vagrant/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/bin/ansible
  python version = 3.10.12 (main, Jun 11 2023, 05:26:28) [GCC 11.4.0] (/usr/bin/python3)
  jinja version = 3.0.3
  libyaml = True
```

Ansible is much more recent with the ppa repository.
