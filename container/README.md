# Execute

everything

## install ansible

```bash
sudo apt update
```

```bash
sudo apt install ansible
```

## Script

### run

```bash
sudo ansible-playbook -i inventory.ini setup_lxc.yml
```

### reset

```bash
sudo ansible-playbook delete_lxc_containers.yml
```
