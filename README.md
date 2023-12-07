# Registry VM
VM with Docker Registry.

## Before you start

Setup your VM using [traefik-vm](https://github.com/andrtell/traefik-vm).

Then:

Create the file `inventory.yaml`.

```
ungrouped:
  hosts:
    vm:
      ansible_host: <HOST>
```

Create the file `vars.yaml`.

```
registry_domain: registry.example.com
```

## Setup VM

Run the playbook `playbooks/01_registry.yaml`.

```
$ ssh-add ~/.ssh/id_ed25519-agent
```

```
$ ansible-playbook -i inventory.yaml \
    --private-key ~/.ssh/id_ed25519-agent \
    --extra-vars "basic_auth_users=$(htpasswd -Bbn myuser mypassword)" \
    --extra-vars "@vars.yaml" \
    playbooks/01_registry.yaml
```

## Podman

Login to your registry.

```
podman login registry.example.com
```

## Images

Example workflow

```
$ podman pull docker.io/httpd
$ podman tag docker.io/httpd registry.example.com/httpd
$ podman push registry.example.com/httpd
$ podman search registry.example.com/httpd
$ podman pull registry.example.com/httpd
```
