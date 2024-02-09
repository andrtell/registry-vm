# Registry VM

Following the instructions in this repository you will configure a VM running Ubuntu 22.04 LTS

Such that:

- A Docker registry is started on the VM.
- Login will be required using Basic Auth (handled by Traefik).

Before you start, you must first complete the setup in [Basic VM](https://github.com/andrtell/basic-vm) and [Traefik VM](https://github.omc/andrtell/traefik-vm). 

## Domain

You need to choose a sub-domain for you Docker registry (e.g. `registry.example.com`). 

The registry domain will be refered to as `<REGISTRY-DOMAIN>`.

The top domain (e.g `example.com`) will be refered to as `<TOP-DOMAIN>`.

## Local setup

### Ansible

Ansible needs to know about your remote machine.

*Before you continue*

Create the file `inventory.yaml` in the root folder of this repo.

```
ungrouped:
  hosts:
    vm:
      ansible_host: <TOP-DOMAIN>
```

## Remote Setup

Basic auth requires user data. The user data holds usernames and a passwords. Here `htpasswd -Bbn <USERNAME> <PASSWORD>` is used to create this data.

*Before you continue*

Run the playbooks

```
ansible-playbook -i inventory.yaml --extra-vars "basic_auth=$(htpasswd -Bbn <USERNAME> <PASSWORD>)" --extra-vars "domain=<REGISTRY-DOMAIN>" playbooks/*.yaml
```

*Then make sure the registry is working*

Login to the registry using Podman.

```
podman login <REGISTRY-DOMAIN> -u <USERNAME> -p <PASSWORD> 
```

**OK, all done!** 
