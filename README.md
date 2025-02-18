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

# 10. Idempotence

1. Install packages with adhoc command

```bash
[vagrant@ansible ema]$ ansible all -b -m package -a '{"name":["git","tree","nmap"],"state":"present"}'
debian | CHANGED => {
    "changed": true,
...
rocky | CHANGED => {
    "changed": true,
...
suse | CHANGED => {
    "changed": true,
...
```

Second run

```bash
[vagrant@ansible ema]$ ansible all -b -m package -a '{"name":["git","tree","nmap"],"state":"present"}' | grep -A 1 "changed"
    "changed": false,
    "name": [
--
    "changed": false
}
--
    "changed": false,
    "msg": "Nothing to do",

```
It is indeed idempotent.

2. Remove packages

```bash
ansible all -b -m package -a '{"name":["git","tree","nmap"],"state":"absent"}' | grep "changed"
    "changed": true,
    "changed": true,
    "changed": true,
```

Second run

```bash
[vagrant@ansible ema]$ ansible all -b -m package -a '{"name":["git","tree","nmap"],"state":"absent"}' | grep "changed"
    "changed": false,
    "changed": false,
    "changed": false,
```

3. Copying files

```bash
[vagrant@ansible ema]$ ansible all -b -m copy -a 'src=/etc/fstab dest=/tmp/test3.txt' | grep "changed"
    "changed": true,
    "changed": true,
    "changed": true,
```

Second run

```bash
[vagrant@ansible ema]$ ansible all -b -m copy -a 'src=/etc/fstab dest=/tmp/test3.txt' | grep "changed"
    "changed": false,
    "changed": false,
    "changed": false,
```

4. Removing files

```bash
[vagrant@ansible ema]$ ansible all -b -m file -a 'path=/tmp/test3.txt state=absent' | grep "changed"
    "changed": true,
    "changed": true,
    "changed": true,
```

Second run

```bash
[vagrant@ansible ema]$ ansible all -b -m file -a 'path=/tmp/test3.txt state=absent' | grep "changed"
    "changed": false,
    "changed": false,
    "changed": false,
```

5. Show main partition usage with `df -h /`

```bash
[vagrant@ansible ema]$ ansible all -b -m command -a 'df -h /' -o
debian | CHANGED | rc=0 | (stdout) Filesystem      Size  Used Avail Use% Mounted on\n/dev/vda3       124G  1.8G  116G   2% /
rocky | CHANGED | rc=0 | (stdout) Filesystem                  Size  Used Avail Use% Mounted on\n/dev/mapper/rl_rocky9-root   70G  2.2G   68G   4% /
suse | CHANGED | rc=0 | (stdout) Filesystem      Size  Used Avail Use% Mounted on\n/dev/sda3       124G  2.3G  118G   2% /
```

We notice that the output is in yellow and that CHANGED is displayed. This is because the output is not idempotent as the command module cannot determine if the output is the same or not.

# 11. Unreachable hosts

*atelier-08*

```bash
[vagrant@ansible ema]$ ansible -m ping all
rocky | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
suse | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
debian | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

We can see that all hosts are reachable. Let's  bippity boppity power off-ity susie.

```bash
$ vagrant halt suse && vagrant global-status
[fog][WARNING] Unrecognized arguments: libvirt_ip_command
==> suse: Attempting graceful shutdown of VM...
id       name           provider state   directory                                                      
--------------------------------------------------------------------------------------------------------
c1f8150  k3smaster      libvirt shutoff /home/skorll/vagrant/ubuntu20.04                               
dd294de  k3smaster2     libvirt running /home/skorll/vagrant/ubuntu20.04                               
12c6109  minecraft-imt2 libvirt running /home/skorll/lpi_code.ipv6_labvm_deploy/vagrant                
4a7159a  rocky          libvirt running /home/skorll/IMT-PracticalAnsible/formation-ansible/atelier-08 
63bf1f4  ansible        libvirt running /home/skorll/IMT-PracticalAnsible/formation-ansible/atelier-08 
d1602ea  debian         libvirt running /home/skorll/IMT-PracticalAnsible/formation-ansible/atelier-08 
18cf8be  suse           libvirt shutoff /home/skorll/IMT-PracticalAnsible/formation-ansible/atelier-08 
 
The above shows information about all known Vagrant environments
on this machine. This data is cached and may not be completely
up-to-date (use "vagrant global-status --prune" to prune invalid
entries). To interact with any of the machines, you can go to that
directory and run Vagrant, or you can use the ID directly with
Vagrant commands from any directory. For example:
"vagrant destroy 1a2b3c4d"
```


```bash
[vagrant@ansible ema]$ ansible-playbook hello-ansible.yml -l testing

PLAY [all] **************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************
ok: [debian]
ok: [rocky]
fatal: [suse]: UNREACHABLE! => {"changed": false, "msg": "Failed to connect to the host via ssh: ssh: connect to host suse port 22: Connection timed out", "unreachable": true}

TASK [debug] ************************************************************************************************************************************************************************
ok: [rocky] => {
    "msg": "Hello Ansible!"
}
ok: [debian] => {
    "msg": "Hello Ansible!"
}

PLAY RECAP **************************************************************************************************************************************************************************
debian                     : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
rocky                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
suse                       : ok=0    changed=0    unreachable=1    failed=0    skipped=0    rescued=0    ignored=0  
```
We see that suse is unreachable. ansible-playbook will continue to run the playbook on the other hosts but will not run it on the unreachable host.

# 12. Playbook

*atelier-09*

I've wrote this playbook to install and enable apache2 on all hosts.
```yaml
# apache-01.yml
- hosts: all
  gather_facts: true
  tasks:
    - name: Refresh apt cache
      ansible.builtin.apt:
        update_cache: yes
        cache_valid_time: 3600
      when: ansible_os_family == 'Debian' or ansible_os_family == 'Ubuntu'

    - name: Install Apache
      ansible.builtin.package:
        name: "{{ 'apache2' if ansible_os_family == 'Debian' or ansible_os_family == 'Ubuntu' else 'httpd' }}"
        state: present
  
    - name: Enable and start apache
      ansible.builtin.service:
        name: "{{ 'apache2' if ansible_os_family == 'Debian' or ansible_os_family == 'Ubuntu' else 'httpd' }}"
        state: started
        enabled: yes
      
    - name: Create a new index.html file
      ansible.builtin.copy:
        dest: /var/www/html/index.html
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Test</title>
            </head>
            <body>
              <h1>My first Ansible-managed website</h1>
            </body>
          </html>
      notify: restart apache

    handlers:
      - name: restart apache
        ansible.builtin.service:
            name: "{{ 'apache2' if ansible_os_family == 'Debian' or ansible_os_family == 'Ubuntu' else 'httpd' }}"
            state: restarted
```

Let's test it.

```bash
[vagrant@ansible ema]$ ansible-playbook apache-01.yml
vagrant@control:~/ansible/projets/ema$ ansible-playbook playbooks/apache-01.yml

PLAY [all] **************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************
ok: [target01]
ok: [target03]
ok: [target02]

TASK [Refresh apt cache] ************************************************************************************************************************************************************
ok: [target03]
ok: [target01]
ok: [target02]

TASK [Install Apache] ***************************************************************************************************************************************************************
ok: [target01]
ok: [target03]
ok: [target02]

TASK [Enable and start apache] ******************************************************************************************************************************************************
ok: [target02]
ok: [target01]
ok: [target03]

TASK [Create a new index.html file] *************************************************************************************************************************************************
ok: [target03]
ok: [target02]
ok: [target01]

PLAY RECAP **************************************************************************************************************************************************************************
target01                   : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target02                   : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target03                   : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

```

Lets curl to check if the website is up.

```bash
vagrant@control:~/ansible/projets/ema$ for host in target01 target02 target03; do curl $host; done
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Test</title>
  </head>
  <body>
    <h1>My first Ansible-managed website</h1>
  </body>
</html>
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Test</title>
  </head>
  <body>
    <h1>My first Ansible-managed website</h1>
  </body>
</html>
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Test</title>
  </head>
  <body>
    <h1>My first Ansible-managed website</h1>
  </body>
</html>
```

Nice, it works. Let's edit the page and start-at-task.

```yaml
# apache-01
...
    - name: Create a new index.html file
      ansible.builtin.copy:
        dest: /var/www/html/index.html
        mode: '0644'
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Test</title>
            </head>
            <body>
              <h1>My very first Ansible-managed website</h1>
            </body>
          </html>
      notify: restart apache

```

```bash
[vagrant@ansible ema]$ ansible-playbook playbooks/apache-01.yml -l testing --start-at-task="Create a new index.html file" -l all

PLAY [all] **************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************
ok: [target03]
ok: [target02]
ok: [target01]

TASK [Create a new index.html file] *************************************************************************************************************************************************
changed: [target02]
changed: [target03]
changed: [target01]

RUNNING HANDLER [restart apache] ****************************************************************************************************************************************************
changed: [target02]
changed: [target03]
changed: [target01]

PLAY RECAP **************************************************************************************************************************************************************************
target01                   : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target02                   : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target03                   : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```
Yaay, it works.

# 13. Adaptive playbook
*atelier-10*
As our previous playbook already took into account the different package managers, we can use it as an adaptive playbook and fix the package name if the package manager is not the same.


```bash
[vagrant@ansible playbooks]$ ansible-playbook apache-global.yml 

PLAY [all] **************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************
ok: [debian]
ok: [rocky]
ok: [suse]

TASK [Refresh apt cache] ************************************************************************************************************************************************************
skipping: [rocky]
skipping: [suse]
changed: [debian]

TASK [Install Apache] ***************************************************************************************************************************************************************
changed: [debian]
changed: [rocky]
changed: [suse]

TASK [Enable and start apache] ******************************************************************************************************************************************************
ok: [debian]
fatal: [suse]: FAILED! => {"changed": false, "msg": "Could not find the requested service httpd: host"}
changed: [rocky]

TASK [Create a new index.html file] *************************************************************************************************************************************************
changed: [debian]
changed: [rocky]

RUNNING HANDLER [restart apache] ****************************************************************************************************************************************************
changed: [debian]
changed: [rocky]

PLAY RECAP **************************************************************************************************************************************************************************
debian                     : ok=6    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
rocky                      : ok=5    changed=4    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
suse                       : ok=2    changed=1    unreachable=0    failed=1    skipped=1    rescued=0    ignored=0  
````

We can see that the playbook is adaptive but does not work on suse because the service name is not the same. We can fix this by adding a condition to the service name.

```yaml
  - name: Enable and start apache
    ansible.builtin.service:
      name: "{{ 'httpd' if ansible_os_family == 'RedHat' else 'apache2' }}"
      state: started
      enabled: yes
...
handlers:
  - name: restart apache
    ansible.builtin.service:
        name: "{{ 'httpd' if ansible_os_family == 'RedHat' else 'apache2' }}"
        state: restarted
```

Let's test it.

```bash
[vagrant@ansible playbooks]$ ansible-playbook apache-global.yml
...
TASK [Enable and start apache] ******************************************************************************************************************************************************
ok: [debian]
ok: [rocky]
changed: [suse]

TASK [Create a new index.html file] *************************************************************************************************************************************************
ok: [debian]
ok: [rocky]
fatal: [suse]: FAILED! => {"changed": false, "checksum": "8790722aac6f41940ba7f9460dd6b5c5bf58f7cc", "msg": "Destination directory /var/www/html does not exist"}
...
```
We can see that we can enable and start service but there is still an error suse. Suse by default use /srv/www/cgi-bin instead of /var/www/html. We can fix this by adding a condition to the copy task.

```yaml
  - name: Create a new index.html file
    ansible.builtin.copy:
      dest: "{{ '/srv/www/cgi-bin/index.html' if ansible_os_family == 'Suse' else '/var/www/html/index.html' }}"
      mode: '0644'
      content: |
        <!doctype html>
        <html>
          <head>
            <meta charset="utf-8">
            <title>Test</title>
          </head>
          <body>
            <h1>My very first Ansible-managed website</h1>
            </body>
        </html>
    notify: restart apache
```

Let's test it.

```bash
[vagrant@ansible playbooks]$ ansible-playbook apache-global.yml
...
TASK [Create a new index.html file] *************************************************************************************************************************************************
ok: [debian]
ok: [rocky]
changed: [suse]

RUNNING HANDLER [restart apache] ****************************************************************************************************************************************************
changed: [suse]

PLAY RECAP **************************************************************************************************************************************************************************
debian                     : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
rocky                      : ok=4    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
suse                       : ok=5    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
```

Nice it works.

# 14. Handlers

After editing we try without the handlers.
```bash
vagrant@control:~/ansible/projets/ema$ ansible-playbook playbooks/apache-01.yml 

PLAY [debian] ***********************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************
ok: [target01]
ok: [target03]
ok: [target02]

TASK [Update package information] ***************************************************************************************************************************************************
changed: [target01]
changed: [target02]
changed: [target03]

TASK [Install Apache] ***************************************************************************************************************************************************************
changed: [target03]
changed: [target01]
changed: [target02]

TASK [Start & enable Apache] ********************************************************************************************************************************************************
ok: [target02]
ok: [target03]
ok: [target01]

TASK [Install custom web page] ******************************************************************************************************************************************************
changed: [target01]
changed: [target02]
changed: [target03]

TASK [Configure redirect] ***********************************************************************************************************************************************************
changed: [target01]
changed: [target02]
changed: [target03]

TASK [Activate redirect] ************************************************************************************************************************************************************
changed: [target03]
changed: [target01]
changed: [target02]

TASK [Reload Apache] ****************************************************************************************************************************************************************
changed: [target01]
changed: [target03]
changed: [target02]

PLAY RECAP **************************************************************************************************************************************************************************
target01                   : ok=8    changed=6    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target02                   : ok=8    changed=6    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target03                   : ok=8    changed=6    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
We can see that it is not idempotent as apache will always be reloaded.


```yaml

- hosts: debian

  tasks:

    - name: Update package information
      apt:
        update_cache: true
        cache_valid_time: 3600

    - name: Install Apache
      apt:
        name: apache2

    - name: Start & enable Apache
      service:
        name: apache2
        state: started
        enabled: true

    - name: Install custom web page
      copy:
        dest: /var/www/html/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Test</title>
            </head>
            <body>
              <h1>My first Ansible-managed website</h1>
            </body>
          </html>

    - name: Configure redirect
      copy:
        dest: /etc/apache2/conf-available/redirect.conf
        content: |
          Redirect /start https://www.startpage.com
      notify: Reload Apache

    - name: Activate redirect
      command:
        cmd: a2enconf redirect
        creates: /etc/apache2/conf-enabled/redirect.conf

  handlers:

    - name: Reload Apache
      service:
        name: apache2
        state: reloaded
```

With this new playbook, we can see that the handlers are now used and the playbook is idempotent.

```bash
vagrant@control:~/ansible/projets/ema$ ansible-playbook playbooks/apache-02.yml
...
PLAY RECAP **************************************************************************************************************************************************************************
target01                   : ok=7    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target02                   : ok=7    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target03                   : ok=7    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

*atelier-11*

We write the following playbook :
``yaml
- hosts: all
  gather_facts: true
  tasks:
    - name: Install chrony
      ansible.builtin.package:
        name: "{{ 'chrony' if ansible_os_family == 'Debian' or ansible_os_family == 'Ubuntu' else 'chrony' }}"
        state: present
    
    - name: Start and enable chrony
      ansible.builtin.service:
        name: "{{ 'chrony' if ansible_os_family == 'Debian' or ansible_os_family == 'Ubuntu' else 'chronyd' }}"
        state: started
        enabled: yes
    
    - name: Configure chrony
      ansible.builtin.copy:
        dest: "{{ '/etc/chrony/chrony.conf' if ansible_os_family == 'Debian' or ansible_os_family == 'Ubuntu' else '/etc/chrony.conf' }}"
        mode: '0644'
        content: |
          # /etc/chrony.conf
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
        notify: restart chrony
  
  handlers:
    - name: restart chrony
      ansible.builtin.service:
        name: "{{ 'chrony' if ansible_os_family == 'Debian' or ansible_os_family == 'Ubuntu' else 'chronyd' }}"
        state: restarted
```

Checking syntax:
```bash
$ ansible-lint ../../playbooks/chronyd.yml  -v
INFO     Executing syntax check on ../../playbooks/chronyd.yml (0.39s)
````
It is correct.

Let's run the playbook.


We check that system clock is synchronized.

```bash
[vagrant@control playbooks]$ ansible all -m shell -a "timedatectl | grep synchronized" --one-line
target01 | CHANGED | rc=0 | (stdout) System clock synchronized: yes
target03 | CHANGED | rc=0 | (stdout) System clock synchronized: yes
target02 | CHANGED | rc=0 | (stdout) System clock synchronized: yes
```

We check that the playbook is idempotent.

```bash
[vagrant@control playbooks]$ ansible-playbook chronyd.yml -l all
...
PLAY RECAP **************************************************************************************************************************************************************************
target01                   : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target02                   : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target03                   : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

It is idempotent.

# 15. Variables
*ateliers-14*
```yaml
# myvars1.yml
- hosts: all
  gather_facts: false
  vars:
    mycar: "Toyota"
    mybike: "Specialized"
  tasks:
    - name: Display the value of mycar
      ansible.builtin.debug:
        msg: "My car is a {{ mycar }}"
    - name: Display the value of mybike
      ansible.builtin.debug:
        msg: "My bike is a {{ mybike }}"
```

```bash
[vagrant@control ema]$ ansible-playbook playbooks/myvars1.yml

PLAY [all] **************************************************************************************************************************************************************************

TASK [Display the value of mycar] ***************************************************************************************************************************************************
ok: [target01] => {
    "msg": "My car is a Toyota"
}
ok: [target02] => {
    "msg": "My car is a Toyota"
}
ok: [target03] => {
    "msg": "My car is a Toyota"
}

TASK [Display the value of mybike] **************************************************************************************************************************************************
ok: [target01] => {
    "msg": "My bike is a Specialized"
}
ok: [target02] => {
    "msg": "My bike is a Specialized"
}
ok: [target03] => {
    "msg": "My bike is a Specialized"
}

PLAY RECAP **************************************************************************************************************************************************************************
target01                   : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target02                   : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target03                   : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

It works like a charm.

```bash
[vagrant@control ema]$ ansible-playbook playbooks/myvars1.yml -e mybike=giant
...
ok: [target01] => {
    "msg": "My bike is a giant"
}
...
[vagrant@control ema]$ ansible-playbook playbooks/myvars1.yml -e mycar='dumpster on wheels'
...
ok: [target01] => {
    "msg": "My car is a dumpster on wheels"
}
...
[vagrant@control ema]$ ansible-playbook playbooks/myvars1.yml -e mycar='BMW',mybike='hypothetical thing'
...
ok: [target01] => {
    "msg": "My car is a BMW"
}
...
ok: [target01] => {
    "msg": "My bike is a hypothetical thing"
}
...
```

We successfully overrided the variables.

Let's try with a set_fact task.

```yaml
# myvars2.yml
- hosts: all
  gather_facts: false

  tasks:
    - name: Set the value of mycar
      ansible.builtin.set_fact:
        mycar: "BMW"
        mybike: "Giant"
      run_once: true
      delegate_to: localhost
    - name: Display the value of mycar
      ansible.builtin.debug:
        msg: "My car is a {{ mycar }} and my bike is a {{ mybike }}"
      run_once: true
      delegate_to: localhost
```

```bash
[vagrant@control ema]$ ansible-playbook playbooks/myvars2.yml 

PLAY [all] **************************************************************************************************************************************************************************

TASK [Set the value of mycar] *******************************************************************************************************************************************************
ok: [target01 -> localhost]

TASK [Display the value of mycar] ***************************************************************************************************************************************************
ok: [target01 -> localhost] => {
    "msg": "My car is a BMW and my bike is a Giant"
}

PLAY RECAP **************************************************************************************************************************************************************************
target01                   : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

Let's see if we can override the variables.

```bash
[vagrant@control ema]$ ansible-playbook playbooks/myvars2.yml -e "mycar=Renault, mybike=Lapierre"

PLAY [all] **************************************************************************************************************************************************************************

TASK [Set the value of mycar] *******************************************************************************************************************************************************
ok: [target01 -> localhost]

TASK [Display the value of mycar] ***************************************************************************************************************************************************
ok: [target01 -> localhost] => {
    "msg": "My car is a Renault, and my bike is a Lapierre"
}

PLAY RECAP **************************************************************************************************************************************************************************
target01                   : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

We successfully overrided the variables.

Let's try with default vars

```yaml
# myvars3.yml
- hosts: all
  gather_facts: false
  tasks:
    - name: Display the value of mycar
      ansible.builtin.debug:
        msg: |
        My car is a {{ mycar | default('VW') }}
        My bike is a {{ mybike | default('BMW') }}
```

```bash
[vagrant@control playbooks]$ ansible-playbook myvars3.yml

PLAY [all] ***************************************************************************************************************************************************************************************************************************************************************************************************

TASK [Display the value of mycar] ****************************************************************************************************************************************************************************************************************************************************************************
ok: [target01] => {
    "msg": "My car is a Toyota\nMy bike is a Specialized\n"
}
ok: [target02] => {
    "msg": "My car is a Toyota\nMy bike is a Specialized\n"
}
ok: [target03] => {
    "msg": "My car is a Toyota\nMy bike is a Specialized\n"
}

PLAY RECAP ***************************************************************************************************************************************************************************************************************************************************************************************************
target01                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target02                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target03                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

```

We edit the inventory to change the printed vales for target02. We edit :
```ini
[testing]
target01
target02 mycar=Mercedes mybike=Honda
target03
```

```bash
[vagrant@control playbooks]$ ansible-playbook myvars3.yml

PLAY [all] ***************************************************************************************************************************************************************************************************************************************************************************************************

TASK [Display the value of mycar] ****************************************************************************************************************************************************************************************************************************************************************************
ok: [target01] => {
    "msg": "My car is a Toyota\nMy bike is a Specialized\n"
}
ok: [target03] => {
    "msg": "My car is a Toyota\nMy bike is a Specialized\n"
}
ok: [target02] => {
    "msg": "My car is a Mercedes\nMy bike is a Honda\n"
}

PLAY RECAP ***************************************************************************************************************************************************************************************************************************************************************************************************
target01                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target02                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target03                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

It works.

We try to prompt the user

```yaml
# display_user.yml
- hosts: all
  gather_facts: false
  vars_prompt:
    - name: user
      prompt: "Enter your username"
      private: no
      default: "microlinux"
    - name: password
      prompt: "Enter your password"
      private: yes
      default: "yatahongaga"
  tasks:
    - name: Display the value of user and password
      ansible.builtin.debug:
        msg: |
          The username is {{ user }}
          The password is {{ password }}
```

```bash
[vagrant@control playbooks]$ ansible-playbook display_user.yml 
Enter your username [microlinux]: 
Enter your password [yatahongaga]: 

PLAY [all] ***************************************************************************************************************************************************************************************************************************************************************************************************

TASK [Display the value of user and password] ****************************************************************************************************************************************************************************************************************************************************************
ok: [target01] => {
    "msg": "The username is microlinux\nThe password is yatahongaga\n"
}
ok: [target02] => {
    "msg": "The username is microlinux\nThe password is yatahongaga\n"
}
ok: [target03] => {
    "msg": "The username is microlinux\nThe password is yatahongaga\n"
}

PLAY RECAP ***************************************************************************************************************************************************************************************************************************************************************************************************
target01                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target02                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target03                   : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

We are prompted for the username and password.

# 16. Stdout and registered variables
*atelier-15*

We write the following playbook :

```yaml
# kernel.yml
- hosts: all
  gather_facts: false
  tasks:
    - name: Display the kernel version
      ansible.builtin.command: uname -a
      register: kernel_version
    - name: Display the kernel version
      ansible.builtin.debug:
        msg: "The kernel version is {{ kernel_version.stdout_lines[0] }}"
```

We run the playbook.

```bash
[vagrant@ansible ema]$ ansible-playbook playbooks/kernel.yml 

PLAY [all] ***************************************************************************************************************************************************************************************************************************************************************************************************

TASK [Display the kernel version] ****************************************************************************************************************************************************************************************************************************************************************************
ok: [debian]
ok: [suse]
ok: [rocky]

TASK [Display the kernel version] ****************************************************************************************************************************************************************************************************************************************************************************
ok: [rocky] => {
    "msg": "The kernel version is Linux rocky 5.14.0-362.13.1.el9_3.x86_64 #1 SMP PREEMPT_DYNAMIC Wed Dec 13 14:07:45 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux"
}
ok: [debian] => {
    "msg": "The kernel version is Linux debian 6.1.0-17-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.69-1 (2023-12-30) x86_64 GNU/Linux"
}
ok: [suse] => {
    "msg": "The kernel version is Linux suse 5.14.21-150500.55.39-default #1 SMP PREEMPT_DYNAMIC Tue Dec 5 10:06:35 UTC 2023 (2e4092e) x86_64 x86_64 x86_64 GNU/Linux"
}

PLAY RECAP ***************************************************************************************************************************************************************************************************************************************************************************************************
debian                     : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
rocky                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
suse                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
```

We try with the var keyword.

```yaml
# kernel.yml
- hosts: all
  gather_facts: false
  tasks:
    - name: Display the kernel version
      ansible.builtin.command: uname -a
      register: kernel_version
    - name: Display the kernel version
      ansible.builtin.debug:
        var: kernel_version.stdout
```

We run the playbook.

```bash
[vagrant@ansible ema]$ ansible-playbook playbooks/kernel.yml 

PLAY [all] ***************************************************************************************************************************************************************************************************************************************************************************************************

TASK [Display the kernel version] ****************************************************************************************************************************************************************************************************************************************************************************
ok: [debian]
ok: [rocky]
ok: [suse]

TASK [Display the kernel version] ****************************************************************************************************************************************************************************************************************************************************************************
ok: [rocky] => {
    "kernel_version.stdout": "Linux rocky 5.14.0-362.13.1.el9_3.x86_64 #1 SMP PREEMPT_DYNAMIC Wed Dec 13 14:07:45 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux"
}
ok: [debian] => {
    "kernel_version.stdout": "Linux debian 6.1.0-17-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.69-1 (2023-12-30) x86_64 GNU/Linux"
}
ok: [suse] => {
    "kernel_version.stdout": "Linux suse 5.14.21-150500.55.39-default #1 SMP PREEMPT_DYNAMIC Tue Dec 5 10:06:35 UTC 2023 (2e4092e) x86_64 x86_64 x86_64 GNU/Linux"
}

PLAY RECAP ***************************************************************************************************************************************************************************************************************************************************************************************************
debian                     : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
rocky                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
suse                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
It also works.

Let's list the number of packages
```yaml
# packages.yml
- hosts: all
  gather_facts: true
  tasks:
    - name: Listing installed packages
      ansible.builtin.shell: rpm -qa | wc -l
      register: installed_packages
      changed_when: false
      when: ansible_os_family == "RedHat" or ansible_os_family == "Suse"
    - name: Display installed packages
      ansible.builtin.debug:
        var: installed_packages.stdout
      when: ansible_os_family == "RedHat" or ansible_os_family == "Suse"
```

Let's test
```bash
[vagrant@ansible ema]$ ansible-playbook packages.yml 

PLAY [all] ******************************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************************************************************************************************
ok: [debian]
ok: [suse]
ok: [rocky]

TASK [Listing installed packages] *******************************************************************************************************************************************************************************************************************
skipping: [debian]
ok: [rocky]
ok: [suse]

TASK [Display installed packages] *******************************************************************************************************************************************************************************************************************
ok: [rocky] => {
    "installed_packages.stdout": "475"
}
skipping: [debian]
ok: [suse] => {
    "installed_packages.stdout": "597"
}

PLAY RECAP ******************************************************************************************************************************************************************************************************************************************
debian                     : ok=1    changed=0    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0   
rocky                      : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
suse                       : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

```

