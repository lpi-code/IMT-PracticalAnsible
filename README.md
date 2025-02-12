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

## 7. Logs

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
We create a new config file `ansible.cfg`
 
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

Checking if logs are working.

```bash
[vagrant@ansible ema]$ ansible all -i rocky,debian,suse -m ping | head -n 3
[WARNING]: Platform linux on host suse is using the discovered Python
interpreter at /usr/bin/python3.6, but future installation of another Python
interpreter could change the meaning of that path. See
https://docs.ansible.com/ansible-
core/2.14/reference_appendices/interpreter_discovery.html for more information.
debian | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
[vagrant@ansible ema]$ cat ~/logs/ansible.log | tail -n 3
    "changed": false,
    "ping": "pong"
}
```

# 8. Inventory

Let's write the inventory file.

```ini
[testing]
rocky
debian
suse

[testing:vars]
ansible_python_interpreter=/usr/bin/python3
ansible_user=vagrant
```

Let's test the inventory file.

```bash
[vagrant@ansible ema]$ ansible all -m ping
debian | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
rocky | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
suse | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```
It works.


Let's try to do some privileged operations : 
```bash
[vagrant@ansible ema]$ ansible all -a "head -n 1 /etc/shadow"
debian | FAILED | rc=1 >>
head: cannot open '/etc/shadow' for reading: Permission deniednon-zero return code
rocky | FAILED | rc=1 >>
head: cannot open '/etc/shadow' for reading: Permission deniednon-zero return code
suse | FAILED | rc=1 >>
head: cannot open '/etc/shadow' for reading: Permission deniednon-zero return code
````

We can fix permission denied with ansible_become. This will make ansible use sudo to run the command.

```bash
[vagrant@ansible ema]$ ansible all -a "head -n 1 /etc/shadow" -e "ansible_become=true"
debian | CHANGED | rc=0 >>
root:!$y$j9T$1UCdnxq34BxNVM1P31I2R1$d6AgxFx72UvS6HpmwGhHWo/d1bfrXtZ/Fds0zJ5oy13:19727:0:99999:7:::
rocky | CHANGED | rc=0 >>
root:!!$6$xDTZIRLhFUOzp2Zh$MpRtbsOkz9Qvbxy1C2k9njrhkNJtSCEy6M8EKpDRsnTqIe4FZqwKatw8jtRYAFxacL6o2ITGNkSqXJOn/2lQi0:19728:0:99999:7:::
suse | CHANGED | rc=0 >>
root:!$6$Vkg2x/wlMzXkunYA$aeTVs1qXTe/Dmoo9unCiOxVqERxQyWaevK/lQEfuceo0oH1WyJqbZ0IDXCLVPLFQZ0C70cLdmxH4IbnH4GReg0:19728::::::
```
It is now working properly.

We can add this to the inventory file.

```ini
[testing]
rocky
debian
suse

[testing:vars]
ansible_python_interpreter=/usr/bin/python3
ansible_user=vagrant
ansible_become=true
```

# 9. Recap (atelier-06)


1. Editing /etc/host to access other vm
```
# /etc/hosts
127.0.0.1      localhost.localdomain  localhost
192.168.56.10  ansible.sandbox.lan    ansible
192.168.56.20  rocky.sandbox.lan      rocky
192.168.56.30  debian.sandbox.lan     debian
192.168.56.40  suse.sandbox.lan       suse
```

We test the connection with the new hostname.

```bash
vagrant@control:~$  ping -c 1 rocky && ping -c 1 debian && ping -c 1 suse && echo all is good
PING rocky.sandbox.lan (192.168.56.20) 56(84) bytes of data.
64 bytes from rocky.sandbox.lan (192.168.56.20): icmp_seq=1 ttl=64 time=1.03 ms
...
all is good
```

2. Config ssh connection to remote host
```bash
ssh-keygen -t rsa
ssh-keyscan -t rsa rocky debian suse >> .ssh/known_hosts
for host in rocky debian suse; do ssh-copy-id $host; done
``` 

3. Installing ansible
`$ sudo apt install ansible`

4. Pinging with ansible
```bash
vagrant@control:~$ ansible all -i rocky,debian,suse -m ping
debian | SUCCESS => {
suse | SUCCESS => {
rocky | SUCCESS => {
```

5. Creating directory `monprojet` with `mkdir monprojet`

6. Creating empty ansible.cfg file `touch monprojet/ansible.cfg`

7. Make sure ansible use the right config file
```bash
$ vagrant@control:~/monprojet$ sudo apt install -y direnv
$ vagrant@control:~/monprojet$ echo 'eval "$(direnv hook bash)"' >> ~/.bashrc
$ vagrant@control:~/monprojet$ source ~/.bashrc
$ vagrant@control:~/monprojet$ direnv edit .
```

We add the following line to the `ANSIBLE_CONFIG` variable in the `.envrc` file `export ANSIBLE_CONFIG=$HOME/monprojet/ansible.cfg`

8. Checking if it works.

```bash
vagrant@control:~/monprojet$ ansible --version | head -n 2
ansible 2.10.8
  config file = /home/vagrant/monprojet/ansible.cfg
```

9. Setting inventory file in `ansible.cfg`

```ini
[defaults]
inventory = $HOME/monprojet/hosts
```

Creating the inventory file. `$ touch $HOME/monprojet/hosts`

10. Enable logs in `ansible.cfg`

```ini
[defaults]
inventory = $HOME/monprojet/hosts
log_path = $HOME/journal/ansible.log
```

11. Testing the logs

```bash
vagrant@control:~/monprojet$ mkdir $HOME/journal
vagrant@control:~/monprojet$ ansible all -i rocky,debian,suse -m ping
vagrant@control:~/monprojet$ tail -n 3 $HOME/journal/ansible.log
    "changed": false,
    "ping": "pong"
}
```

12. Populate the inventory file

```ini
[testlab]
rocky
debian
suse
```
Testing the inventory file.

```bash
vagrant@control:~/monprojet$ ansible testlab -m ping
debian | SUCCESS => {
suse | SUCCESS => {
rocky | SUCCESS => {
```

13. Forcing ansible user to be vagrant

Editing hosts
```ini
[testlab:vars]
ansible_user=vagrant
```

14. Testing the connection.

```bash
vagrant@control:~/monprojet$ ansible all -m ping
debian | SUCCESS => {
suse | SUCCESS => {
rocky | SUCCESS => {
```

15. Setting up privilege escalation

```ini
[testlab:vars]
ansible_user=vagrant
ansible_become=true
```
in the inventory file.

16. Testing privilege escalation by showing first line of /etc/shadow

```bash
vagrant@control:~/monprojet$ ansible all -a "head -n 1 /etc/shadow"
ansible all -a "head -n 1 /etc/shadow"
rocky | CHANGED | rc=0 >>
root:*:19579:0:99999:7:::
debian | CHANGED | rc=0 >>
root:*:19579:0:99999:7:::
suse | CHANGED | rc=0 >>
root:*:19579:0:99999:7:::
```
No error, it works.

