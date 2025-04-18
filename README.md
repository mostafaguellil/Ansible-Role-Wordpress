# Ansible Role: WordPress

## Overview
This role installs and configures a complete WordPress stack (Apache, PHP, MariaDB, WordPress) on either Ubuntu or Rocky Linux. It is idempotent, uses variables for customization, and includes handlers to restart services when needed.

## Example Playbook

Create a playbook `deploy_wordpress.yml` in your project root:

```yaml
---
- name: Deploy WordPress
  hosts: all
  become: yes

  roles:
    - wordpress
```

## Usage

```bash
ansible-playbook -i /root/config/inventaire /root/config/playbooks/deploy_wordpress.yml
```

This will:
1. Update package cache and install Apache, PHP, MariaDB, wget, unzip.
2. Start & secure MariaDB, create the WordPress database and user.
3. Download and unpack WordPress.
4. Set appropriate file permissions.
5. Deploy and enable an Apache VirtualHost for WordPress.
6. Restart Apache when needed via a handler.

## Handlers

```yaml
- name: Restart Apache
  ansible.builtin.service:
    name: "{{ apache_service_name_debian if ansible_os_family == 'Debian' else apache_service_name_redhat }}"
    state: restarted
```

## Testing

You can test locally in containers:

```bash
# Ubuntu
docker run -it --rm ubuntu:22.04 bash -c "\
  apt update && apt install -y python3 python3-pip && \
  pip3 install ansible && \
  ansible-playbook -i localhost, --connection=local deploy_wordpress.yml"

# Rocky Linux
docker run -it --rm rockylinux:9 bash -c "\
  dnf install -y python3 python3-pip && \
  pip3 install ansible && \
  ansible-playbook -i localhost, --connection=local deploy_wordpress.yml"
```

## Author
Mostafa GUELLIL