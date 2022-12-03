# configure-dana-dev
The purpose of this repository is to take a new Red Hat Enterprise Linux-based VM and turn it into a `dana-dev-x` VM using an `Ansible` playbook.

# Getting Started
## Prerequisites
- A local machine with `Ansible` installed. 
- This was tested on a `Red Hat Enterprise Linux 8.7` VM with the following version of `Ansible` installed:

```
ansible [core 2.13.3]
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/home/dana/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3.9/site-packages/ansible
  ansible collection location = /home/dana/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/bin/ansible
  python version = 3.9.13 (main, Nov  9 2022, 13:16:24) [GCC 8.5.0 20210514 (Red Hat 8.5.0-15)]
  jinja version = 3.1.2
  libyaml = True
```
- A Virtual Machine (on `Azure` for example) with OS Disk space of `256GB`. Note it may be needed to extend the root partition for the playbook to run successfully, [as explained here](https://learn.microsoft.com/en-us/azure/virtual-machines/linux/expand-disks?tabs=azure-cli%2Crhellvm#increase-the-size-of-the-os-disk).
- SSH passwords to the VMs you'd like to configure.

## Steps

- In order to run the playbook, clone it to your local machine:

```
$ git clone https://github.com/dana-team/configure-dana-dev.git
$ cd configure-dana-dev
```
- Edit the `inventory.ini` file to include the VMs you want to configure.
- Establish SSH connection between your local host and the VMs you want to configure:

```
$ ssh-keygen
$ ssh-copy-id <user>@<remote-vm>
```

- Test the SSH connection works correctly:

```
$ ansible -m ping all -i inventory.ini

dana-dev-x.westeurope.cloudapp.azure.com | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/libexec/platform-python"
    },
    "changed": false,
    "ping": "pong"
}
```

### Configure
- Run the playbook:

```
$ ansible-playbook configure-vm.yml -i inventory.ini --ask-vault-pass 
```

- The `Ansible Vault Password` exists as a `repository secret` in this repo (`Settings` -> `Secrets` - > `Repository secrets`).

### Unconfigure
- Run the playbook:

```
$ ansible-playbook unconfigure-vm.yml -i inventory.ini
```

# What gets configured
- The `configure-vm.yml`/`unconfigure-vm.yml` playbooks do the following:
    1. Register/Unregister the VM to a Red Hat subscription and enables other repos.
    2. Intsall/Uninstall DNF packages.
    3. Intsall/Uninstall certain dev tools.
    4. Install/Uninstall GoLand.

## How to add/remove things
- Edit the `group_vars/dana-dev-vms.yml` file to add/remove packages, change versions, etc...