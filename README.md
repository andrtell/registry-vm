# Registry VM
VM with Docker Registry.

## Traefik VM

Setup you VM using [traefik-vm](https://github.com/andrtell/traefik-vm).

## Ansible

Create the file `inventory.yaml`.

```
ungrouped:
  hosts:
    vm:
      ansible_host: vm01
```

Create the file `vars.yaml`.

```
registry_domain: registry.example.com
cert_from_file: true
```

## Registry VM

Run the playbook `playbooks/01_registry.yaml`.

```
ansible-playbook -i inventory.yaml --extra-vars "basic_auth_users=$(htpasswd -Bbn myuser mypassword)" --extra-vars "@vars.yaml" playbooks/01_registry.yaml
```

## Podman

Login to your registry.

```
podman login registry.example.com
```

## Images

*Example workflow*

Push an image to the new registry.

```
podman pull docker.io/httpd
```

```
podman tag docker.io/httpd registry.example.com/httpd
```

```
podman push registry.example.com/httpd
```

Deploy the image.

```
podman -r -c vm01 login registry.example.com
```

```
podman -r -c vm01 run -d --rm --name httpd --network traefik \
    --label 'traefik.enable=true' \
    --label 'traefik.http.routers.myrouter.rule=PathPrefix(`/`)' \
    registry.example.com/httpd
```
