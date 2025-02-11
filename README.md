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

## 3. Install with pip

```bash
$ python3 -m venv ansible
$ source ansible/bin/activate
(ansible) $ pip install ansible
```
We check the version

```bash
(ansible) [vagrant@rocky ~]$ ansible --version
ansible [core 2.15.13]
  config file = None
  configured module search path = ['/home/vagrant/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /home/vagrant/ansible/lib64/python3.9/site-packages/ansible
  ansible collection location = /home/vagrant/.ansible/collections:/usr/share/ansible/collections
  executable location = /home/vagrant/ansible/bin/ansible
  python version = 3.9.18 (main, Sep  7 2023, 00:00:00) [GCC 11.4.1 20230605 (Red Hat 11.4.1-2)] (/home/vagrant/ansible/bin/python3)
  jinja version = 3.1.5
  libyaml = True
```

## 4. Ping ansible

We setup atelier-02

```
[vagrant@ansible ~]$ ansible all -i debian,rocky,suse -u vagrant -m ping
debian | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
[WARNING]: Platform linux on host suse is using the discovered Python interpreter at /usr/bin/python3.6, but future installation of another
Python interpreter could change the meaning of that path. See https://docs.ansible.com/ansible-
core/2.14/reference_appendices/interpreter_discovery.html for more information.
suse | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3.6"
    },
    "changed": false,
    "ping": "pong"
}
rocky | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
[vagrant@ansible ~]$ ansible all -i debian,rocky,suse -u vagrant -m ping
debian | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
rocky | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
[WARNING]: Platform linux on host suse is using the discovered Python interpreter at /usr/bin/python3.6, but future installation of another
Python interpreter could change the meaning of that path. See https://docs.ansible.com/ansible-
core/2.14/reference_appendices/interpreter_discovery.html for more information.
suse | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3.6"
    },
    "changed": false,
    "ping": "pong"
}
```

All host are on success

## 5. Ping with atelier-03

```
[vagrant@control ~]$ ansible all -i target01,target02,target03 -u vagrant -m ping
target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
...
```

It works

## 6. Install direnv

```
vagrant@debian:~/monprojet$ echo "export TESTVAR=Yatahongaga" > .envrc
direnv: error /home/vagrant/monprojet/.envrc is blocked. Run `direnv allow` to approve its content
vagrant@debian:~/monprojet$ direnv allow
direnv: loading ~/monprojet/.envrc
direnv: export +TESTVAR
vagrant@debian:~/monprojet$ echo "export TESTVAR=Yatahongaga" > .envrc
direnv: loading ~/monprojet/.envrc
direnv: export +TESTVAR
vagrant@debian:~/monprojet$ cat .envrc 
export TESTVAR=Yatahongaga
```

I had to run `direnv allow` to approve the content of the `.envrc` file.

```
[vagrant@rocky ~]$ echo "export TESTVAR=Yatahongaga" > .envrc
direnv: error /home/vagrant/.envrc is blocked. Run `direnv allow` to approve its content
[vagrant@rocky ~]$ direnv allow
direnv: loading ~/.envrc
direnv: export +TESTVAR
[vagrant@rocky ~]$ echo $TESTVAR
Yatahongaga
```
It also works on rocky

## 7. Inventory

```
[vagrant@ansible ~]$ ansible all -i debian,rocky,suse -m ping
debian | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
rocky | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
[WARNING]: Platform linux on host suse is using the discovered Python interpreter at /usr/bin/python3.6, but future installation of another
Python interpreter could change the meaning of that path. See https://docs.ansible.com/ansible-
core/2.14/reference_appendices/interpreter_discovery.html for more information.
suse | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3.6"
    },
    "changed": false,
    "ping": "pong"
}
```

It works on atelier-05 we are ready to go to the next step.
We create a new inventory file `ansible.cfg`
 
```
[vagrant@ansible ema]$ touch ~/.ansible.cfg
[vagrant@ansible ema]$ ansible --version | head -n 2
ansible [core 2.14.17]
  config file = /home/vagrant/.ansible.cfg
```


We export ANSIBLE_CONFIG

``` bash 
`[vagrant@ansible ema]$ export ANSIBLE_CONFIG=$(expand_path ansible.cfg)
-bash: expand_path: command not found

```

Expand_path is not found, we will use `pwd` instead.

```bash
[vagrant@ansible ema]$ export ANSIBLE_CONFIG=$(pwd)/ansible.cfg
[vagrant@ansible ema]$ echo $ANSIBLE_CONFIG
/home/vagrant/ansible/projets/ema/ansible.cfg
```


I had trouble running the command `direnv allow` because the `.envrc` file was not found. I had to create it first.

```
[vagrant@ansible ema]$ direnv allow
direnv: error .envrc file not found
[vagrant@ansible ema]$ touch .envrc
direnv: error /home/vagrant/ansible/projets/ema/.envrc is blocked. Run `direnv allow` to approve its content
[vagrant@ansible ema]$ direnv allow
direnv: loading ~/ansible/projets/ema/.envrc
```

I know write ANSIBLE_CONFIG in the `.envrc` file.

```bash
[vagrant@ansible ema]$ echo "export ANSIBLE_CONFIG=$(pwd)/ansible.cfg" > .envrc
direnv: error /home/vagrant/ansible/projets/ema/.envrc is blocked. Run `direnv allow` to approve its content
[vagrant@ansible ema]$ direnv allow
direnv: loading ~/ansible/projets/ema/.envrc
```

We write a simple inventory file.

``` ini
#~/ansible/projets/ema/anisble.cfg
[defaults]
inventory = ./inventory
log_path = ~/logs
```

We create the logs directory.

```bash
[vagrant@ansible ema]$ mkdir logs
```

We create the inventory file.


