PolymerOps
==============
A DevOps framework for Polymer.

Introduction
------------
PolymerOps is a DevOps project for [Polymer] that automates tasks 
like running, deploying and configuring Polymer server 
environments from development to production, as well as 
providing a local development environment VM (virtual machine)
for building your web app. Products like
[Firebase](https://firebase.google.com) and 
[GoogleCloud](https://cloud.google.com) are awesome pieces of
technology, but for deployment on other servers where you 
control the infrastructure yourself, then PolymerOps provides 
a strong structure for that.

PolymerOps contains the following pre-configured tools for 
use with Polymer:

- [Vagrant] for local development on a virtual server.
- [Ansible] for all provisioning and configuration management.

Setup
--------
1. [Install Vagrant](https://www.vagrantup.com/downloads.html)

2. [Install Ansible](http://docs.ansible.com/ansible/intro_installation.html)
(Optional). Ansible is installed on the Vagrant VM by default, so 
can be run from there

3. Clone PolymerOps project (replace `project-name` with project name):

   ```git clone git@github.com:WillSquire/PolymerOps.git project-name --depth=1 --branch=master```

4. Move into chosen `project-name` folder and delete the `.git` 
directory:
    
    ```cd project-name && rm -rf .git```

5. Add existing polymer project or create a new one 
[using the Polymer CLI](https://www.polymer-project.org/1.0/start/toolbox/set-up)
into the `/app` folder.

Stack
-----
Currently built for Ubuntu. Servers require this OS. 
Here's the fully deployed stack:

- Ubuntu 16.04 LTS (Xenial Xerus)
- Apache
    - mod_deflate
    - mod_expires
    - mod_headers
    - mod_http2 (HTTP2 Push compatible)
    - mod_rewrite
    - mod_socache_shmcb (Key-value caching)
- Node
- NPM
    - n (Node version control)
    - Bower
    - Polymer-CLI

Configuration
-------------
### Vagrant
PolymerOps avoids setting the same variables in multiple 
locations. As such, Vagrant reads Ansible's config 
files (`dev.yml` and `hosts.yml`) in the `Vagrantfile`. Thus, 
these files should be kept in YAML format. To change the Vagrant 
VM's IP address for example, open up `hosts.yml` and set the 
`dev` host group IP:

    dev:
      hosts:
        192.168.33.12:

To change the development VM's domain, set the `domain` variable 
in `dev.yml`:

    domain: example.dev

### Ansible
As per Ansible's 
[documentation](http://docs.ansible.com/ansible/index.html),
machine group variables go in the `group_vars/*` directory and 
use the group name as the file name (i.e. `dev.yml`). Development, 
staging and production variables are stored in the following 
files that correspond to their environment:

- dev.yml
- staging.yml
- production.yml
 
These files should be stored inside of the `group_vars` 
directory for Ansible to find them. Because these files contain 
sensitive information, all (apart from dev variables) are 
ignored by GIT by default.

`hosts/hosts.yml` contain the server details and what group 
they belong to. These have been done in YAML format so
Vagrant can read them (see Ansible's 
[example of YAML format for hosts](https://github.com/ansible/ansible/blob/devel/examples/hosts.yaml)).

Below are details regarding the configuration performed by 
Ansible for each piece of technology installed:

#### Apache
Polymer handles 404s on the frontend, as such Apache is setup to 
redirect all traffic without a file extension (or requests 
that don't contain a `.` followed by at least one character) 
to the app entry point (`index.html` by default).

#### UFW
Firewall defaults to `deny` all traffic in and `allow` all 
traffic out on all ports apart from `22`, `80` and `443` on `tcp`, 
which are set to `limit`, `allow` and `allow` respectively.

Usage
-----
### Development
To start the local development VM, run:

    vagrant up
    
Update changes from the Vagrant config (*remember: Ansible 
variables can affect this, i.e. development IP*):

    vagrant reload

Update changes from the Ansible config (i.e. HTTP port):

    vagrant provision

In the Vagrantfile there is an rsync (one way - host machine to 
VM guest) setting that can be used over the default (two way) 
setting. It is currently the fastest way to sync, but does remove
sync capability from VM guest to host machine. If using the rsync
method instead, run the following to automatically sync files upon 
change:

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
    
Future
------
- Test Windows support (so far tested on Mac. Linux *shouldn't* have issues)
- Add Polymer-CLI build upon deploying to production
- Add conditional logic for Ansible to prevent it saying 'changed' when the state hasn't (i.e. logs creation)
- Add fail2ban
- Consider switching Apache to NGINX when NGINX has HTTP2 Push support
    
License
-------
PolymerOps uses a BSD-like license that is available 
[here](./LICENSE.txt).

[polymer]: https://www.polymer-project.org "Polymer"
[ansible]: https://www.ansible.com "Ansible"
[vagrant]: https://www.vagrantup.com "Vagrant"