# ripple-rest-ansible

This role installs and configures a ripple-rest server. The user can modify the configuration parameters to more closely 
match their production environment. Any number of servers and environments (e.g., production, staging) 
can be added with separate configurations.

## Requirements

This role requires Ansible 1.4 or higher and requires Ubuntu (currently tested on [Trusty64](http://releases.ubuntu.com/14.04/)).

## Default Configurations

Configurations are defined at the role level and inventory level. For specifying configurations that are different across hosts or groups (e.g., production vs. staging) it is recommended to define these as group variables and reference them in the individual roles.

Default configurations for can be found in the [`group_vars/all/main.yml`](group_vars/all/main.yml).
These variables are available at the role level.

## Playbook Roles

### Common

The tasks in this role are generic and required in any environment and platform.

### NGINX

NGINX is configured as a reverse-proxy in front of the ripple-rest node process.

#### SSL Certificates

NGINX is configured to terminate SSL connections and requires valid SSL certificates. 
SSL settings can be found in  [`group_vars/all/main.yml`](group_vars/all/main.yml).

```yaml
# group_vars/all/main.yml
rest:
    server:
      listen: 443
      server_name: "{{ rest.url_base }}"
      ssl: "on"
      ssl_certificate: "/etc/nginx/ssl/server.crt"
      ssl_certificate_key: "/etc/nginx/ssl/server.key"
      ssl_protocols: "TLSv1 TLSv1.1 TLSv1.2"
      ssl_ciphers: "ECDHE-RSA-AES256-SHA:DHE-RSA-AES256-SHA:DHE-DSS-AES256-SHA:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA"
```

SSL certificates should be purchased and managed on the server outside of Ansible.

### NodeJS

NodeJS is installed using the [Ansible role][3] published by NodeSource

### Ripple-Rest

The Ripple-Rest role follows closely the [configuration instructions][4] for deployment in the Ripple developer portal.
For understanding how the playbook specifies the Ripple-Rest version to deploy, see [Running the Playbook](#running-the-playbook).

## Inventory

When working with multiple environments, it is [best practice][1] to keep the inventory files separate.
For managing dynamic inventory (e.g., AWS) [see the Ansible documentation][2].

## Running the Playbook

Provisioning and starting a server is simply:

```shell
$ ansible_playbook ripple-rest.yml -i staging.yml
```

where `staging.yml` is a hosts file for the staging servers.

See [`group_vars/all/main.yml`](group_vars/all/main.yml) for the default release version

By default, the `ripple-rest.yml` playbook **will update** code on the server. To disable this, simply pass `update=false` on the command-line.

### Provisioning without updating

```shell
$ ansible_playbook ripple-rest.yml -i staging.yml --extra-vars "update=false"
```

### Specifying a version
To specify a version to deploy, use the `version` variable:

```shell
$ ansible_playbook ripple-rest.yml -i staging.yml --extra-vars "version=master"
```

The version can be the full 40-character SHA-1 hash, the literal string HEAD, a branch name, or a tag name for the [ripple-rest repository][5].
See http://docs.ansible.com/git_module.html#examples

## Testing and Developmnt

Testing requires [Vagrant][6] and [VirtualBox][7].

After installing these, just run `vagrant up` from the root of this project.

```shell
$ vagrant up
```

See [Vagrant-Ansible][8] documentation for more configuration options.



[1]: http://docs.ansible.com/playbooks_best_practices.html#stage-vs-production "Stage vs. Production"
[2]: http://docs.ansible.com/intro_dynamic_inventory.html "Dynamic inventory"
[3]: https://github.com/nodesource/ansible-nodejs-role "NodeSource NodeJS Ansible Role"
[4]: https://ripple.com/build/ripple-rest/ "Ripple-REST Developer Portal"
[5]: https://github.com/ripple/ripple-rest "Ripple-REST Github Repository"
[6]: https://www.vagrantup.com/ "Vagrant"
[7]: https://www.virtualbox.org/ "VirtualBox"

[8]: https://docs.vagrantup.com/v2/provisioning/ansible.html "Vagrant & Ansible Configuration"
