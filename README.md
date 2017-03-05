PolymerOps
==============
A DevOps framework for Polymer.

Introduction
------------
PolymerOps is a DevOps project for [Polymer] that automates tasks 
like running, deploying and configuring Polymer from development 
to production environments, as well as providing a local 
development virtual machine (vm). Allowing for a faster and safer 
development pipeline. 

PolymerOps contains the following pre-configured tools:

- [Vagrant] for local development on a virtual server.
- [Ansible] for all provisioning and configuration management.

Stack
-----

Vagrant uses Ubuntu as the OS and Ansible has been configured for
use with a Ubuntu system (with aim to branch out in future). Here's
the full stack:

- Ubuntu 16
- Apache
    - mod_deflate
    - mod_expires
    - mod_headers
    - mod_http2 (HTTP2 Push compatible)
    - mod_socache_shmcb
- Node
- Bower
- Polymer-CLI

Installation
------------

1. [Install Vagrant](https://www.vagrantup.com/downloads.html)

2. [Install Ansible](http://docs.ansible.com/ansible/intro_installation.html)
(Optional). Ansible is installed on the Vagrant VM by default, so 
can be run from there

3. Clone PolymerOps project (replace `project-name` with your project name):

   ```git clone git@github.com:WillSquire/PolymerOps.git project-name --depth=1 --branch=master```

4. Move into your `project-name` folder and delete the `.git` 
directory so you can setup your own version control:
    
    ```cd project-name && rm -rf .git```

5. Add an existing polymer project to the `/app` folder, or create
a new one using the Polymer CLI as instructed 
[here](https://www.polymer-project.org/1.0/start/toolbox/set-up).


Configuration
-------------
### Vagrant
PolymerOps has been designed to set variables in one 
place. As such, Vagrant uses Ansible's config files (`dev.yml` 
and `hosts.yml`) to configure the VM. 

For example, to change the Vagrant VM's IP address open up 
`hosts.yml` and set the `dev` host group IP:

    dev:
      hosts:
        192.168.33.12:

To change the development VM's domain, set the `domain` variable 
in `dev.yml`:

    domain: example.dev

To achieve this, Ansible's variables need to be kept in YAML 
format. By doing this, Vagrant and Ansible don't need to be 
'married up' from both sides.

### Ansible
As per Ansible's 
[documentation](http://docs.ansible.com/ansible/index.html),
machine group variables go in the `group_vars/*` directory and 
use the group name as the file name (i.e. `dev.yml`).
 
Development, staging and production variables are stored in 
the following files that correspond to their environment:

- dev.yml
- staging.yml
- production.yml
 
These files should be stored inside of the `group_vars` 
directory for Ansible to find them. Because these files contain 
sensitive information, all (apart from dev variables) are 
ignored by GIT by default.

Usage
-----
### Development
To start the local development VM, run:

    vagrant up

Vagrant uses rsync one way (host machine to VM guest) to sync
the project to the VM (as this is currently the fastest method). 
Other methods are included in the Vagrantfile if 2-way sync is 
needed. To automatically sync on file changes, run:

    vagrant rsync-auto

### Depolyment
To deploy the project to all machines execute the following command:

    ansible-playbook deploy.yml

To deploy only on machines that are listed in a group (i.e. `staging`), 
execute the command for the corresponding environment:

Dev:

    ansible-playbook deploy.yml -l dev

Staging:

    ansible-playbook deploy.yml -l staging
    
Production:

    ansible-playbook deploy.yml -l production
    
Todo
----
- Add HTTP2 Push (Apache is already setup, just needs Polymer config i.e. add `rel="preload"` to links where needed)
- Test Windows support (so far tested on Mac. Linux *shouldn't* have issues)
    
License
-------
PolymerOps uses a BSD-like license that is available 
[here](./LICENSE.txt).

[polymer]: https://www.polymer-project.org "Polymer"
[ansible]: https://www.ansible.com "Ansible"
[vagrant]: https://www.vagrantup.com "Vagrant"