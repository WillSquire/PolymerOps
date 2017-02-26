PolymerOps
==============
A DevOps framework for Polymer.

Introduction
------------
PolymerOps is a DevOps project for [Polymer] that 'wraps' your 
Polymer project up to automate tasks like running, deploying and 
configuring Polymer from development to production environments. 
Allowing for a faster and safer development pipeline. 

PolymerOps contains the following pre-configured tools:

- [Vagrant] for local development on a virtual server.
- [Ansible] for all provisioning and configuration management.

Setup
-----
### Installation

- Install [Vagrant] by following the install instructions
[here](https://www.vagrantup.com/downloads.html).

- (Optionally) install [Ansible] on your machine 
[here](http://docs.ansible.com/ansible/intro_installation.html). 
Ansible is installed on the Vagrant VM by default during provisioning. However to run 
Ansible outside of the VM, installation is required.

- Create a new PolymerOps project into a new folder (replace `project-name` with your project name):

  ```git clone git@github.com:WillSquire/PolymerOps.git project-name --depth=1 --branch=master```

- Once download, it's advisable to delete the `.git` directory so you
can setup your own version control.

- Add existing polymer project to the `/app` folder, or create
a new one using the Polymer CLI as instructed 
[here](https://www.polymer-project.org/1.0/start/toolbox/set-up).


### Configuration
Add project variables to the `group_vars/*` files that contain the variables used for each given environment. These variables are stored in the following files that correspond to their environment:

- dev.yml
- staging.yml
- production.yml
 
These files should be stored inside of the `group_vars` directory for Ansible to find them. 

These files contain sensitive information, so are ignored by GIT by default.

Usage
-----
### Deployment
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
- Add HTTP2 Push support (Apache is already setup, just needs config)
- Test Windows support (so far tested on Mac. Linux *shouldn't* have issues)
    
License
-------
PolymerOps uses a BSD-like license that is available 
[here](./LICENSE.txt).

[polymer]: https://www.polymer-project.org "Polymer"
[ansible]: https://www.ansible.com/ "Ansible"
[vagrant]: https://www.vagrantup.com/ "Vagrant"