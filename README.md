Ansible role for GitLab (Omnibus)
=========

Install GitLab Community Edition (CE) and Enterprise Edition (EE) using Linux packages and configure the instance.


Requirements
------------

- [dnf_versionlock](https://docs.ansible.com/projects/ansible/latest/collections/community/general/dnf_versionlock_module.html) module from `community.general` collection (version 13.4.0). 


Role Variables
--------------

A description of the settable variables for this role (see `defaults/main.yml` and `vars/main.yml`).

### Choose the GitLab edition
Select the GitLab edition to install using the variable `gitlab_edition`:
```yaml
gitlab_edition: 'ce' # Community Edition (default)
gitlab_edition: 'ee' # Enterprise Edition
```

### Choose the version
Use `gitlab_version` variable to install a specific version of GitLab.

An example:
```yaml
gitlab_version: "19.1.0"
```

By default, the variable is set to empty, meaning the role installs the latest GitLab release available in the official GitLab repository.
```yaml
gitlab_version: ""
```

References:
- Supported platforms: https://docs.gitlab.com/install/package/
- Package browser: https://packages.gitlab.com/ui/browse/gitlab/


### Configure GitLab at installation time
Use the dictionary `gitlab_installation_vars` to define the environment variables useful to configure GitLab at installation time.
They are:
- `EXTERNAL_URL`: sets the external URL for the instance, it is optional but recommended;
- `GITLAB_ROOT_EMAIL`: sets the custom email for the root account;
- `GITLAB_ROOT_PASSWORD`: sets the custom password for the root account.

Note that these variables only work during the first installation and have no effect on subsequent reconfigure runs.

An example:
```yaml
gitlab_installation_vars:
  EXTERNAL_URL: "http://gitlab.example.com"
  GITLAB_ROOT_EMAIL: "admin@example.com"
  GITLAB_ROOT_PASSWORD: "strong-password"
```

By default, the dictionary is empty, so the configuration does not happen during the first installation.
```yaml
gitlab_installation_vars: {}
```

Consult the official documentation for more info about configuring GitLab during the first installation.

References:
- https://docs.gitlab.com/install/package/debian/#advanced-configuration
- https://docs.gitlab.com/install/package/ubuntu/#advanced-configuration

### Download `initial_root_password` file
Setup the variable `gitlab_fetch_initial_root_password=true` to download the file using the `fetch` module.
By default, the role does not download the file.
```yaml
gitlab_fetch_initial_root_password: false
```

Use the variable `gitlab_fetch_initial_root_password_dest` to specify a destination path where to save the 'initial_root_password' file.
```yaml
gitlab_fetch_initial_root_password_dest: /tmp
```

By default, `fetch` module is setup with parameter `flat=false`, so it will append 'hostname/path/to/file' the destination path (see `fetch` module's parameter [flat](https://docs.ansible.com/projects/ansible/latest/collections/ansible/builtin/fetch_module.html#parameter-flat)).


### Configure GitLab using a static `gitlab.rb` file
Use the variable `gitlab_config_file` to replace the `gitlab.rb` file on the instance with a custom configuration file using the Ansible module `copy`. 
This option allows you to upload your custom configuration file that you choose to store inside `files` directory of this role, or in a sub-directory of your Ansible project's `inventory_dir` path.

An example:
```yaml
gitlab_custom_config_file: gitlab.custom.rb
```

By default, the variable is set to empty, meaning the role keeps the file `gitlab.rb` on the instance untouched.

```yaml
gitlab_custom_config_file: ""
```

Consult the official documentation for more info about [configuring a Linux package installation](https://docs.gitlab.com/omnibus/settings/) using `gitlab.rb` file.


Dependencies
------------

None.


Example Playbook
----------------

The role uses the following tags in order to run specific tasks:
- `install`: to install the GitLab package;
- `config`: to update the configuration file (`gitlab.rb`) and reconfigure the instance;
- `checks`: to perform checks on a running instance.

```yaml
# Usage:
#   ansible-playbook -l <host/group> gitlab.yml [--tags TAGS]
---
- name: Install and configure GitLab CE
  hosts: all
  become: true

  roles:
    - mcaliandro.gitlab_ce
```


Compatibility
-------------

This role has been tested on these Linux distributions:
- Ubuntu Server LTS 22.04
- Ubuntu Server LTS 24.04
- Ubuntu Server LTS 26.04


License
-------

Apache License


Author Information
------------------

Created in 2026 by Matteo Caliandro, <mcaliandro.dev@gmail.com>