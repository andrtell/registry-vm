# Registry VM

Use [Ansible](https://docs.ansible.com/ansible/latest/index.html) to configure a remote machine running Ubuntu 22.04 LTS.

Such that:

- A Docker registry is installed and started as a SystemD user service.
- The registry will require a login using Basic Auth (handled by Traefik).

This repo requires you to first setup you VM using [Basic VM](https://github.com/andrtell/basic-vm) and then [Traefik VM](https://github.omc/andrtell/traefik-vm). 

## Domain

This repo assumes you have setup a domain with a wildcard sub-domain (see [Traefik VM](https://github.omc/andrtell/traefik-vm)).

*Before you continue*

Pick a sub-domain for your registry (e.g. `registry.example.com`). There is no need to add a separate sub-domain to your DNS when using wild-card subdomains.

## Local setup

### Ansible

Ansible needs to know about your remote machine.

*Before you continue*

Create the file `inventory.yaml` in the root folder of this repo.

```
ungrouped:
  hosts:
    vm01:
      ansible_host: <DOMAIN>
```

(Replace `<DOMAIN>` with your top domain e.g. `example.com`).

## Remote Setup

Basic auth requires `user-file` (just a string in our case). To create the `user-file` we use `htpasswd`.

*Before you continue*

Run the playbooks

```
ansible-playbook -i inventory.yaml --extra-vars "basic_auth=$(htpasswd -Bbn <USER> <PASSWORD>)" --extra-vars "registry_domain=<REGISTRY-DOMAIN>" playbooks/*.yaml
```

(Replace `<REGISTRY-DOMAIN>` with your registry domain e.g. `registry.example.com`).

## Test it

Login to the registry using Podman.

```
podman login <REGISTRY-DOMAIN> -u <USER> -p <PASSWORD> 
```

**OK, all done!** 
