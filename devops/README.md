DevOps
==============
[Ansible] is the devOps tool used for provisioning and configuration management. 

Setup
-----
### Install Ansible
Install [Ansible] on your local machine (not server):

    sudo pip install ansible
    
Alternatively, try the following with Mac if the above fails:

    brew install ansible

Ensure you have the latest version (2.2+):

    brew update && brew upgrade ansible

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
To deploy the project to all machines in the `webservers` and `dbservers` groups (excluding those also in `dev`), execute the following command:

    ansible-playbook deploy.yml

To perform the above deployment only on machines that are also listed in another group (i.e. `staging`), execute the following command for the corresponding environment:

Staging:

    ansible-playbook deploy.yml -l staging

[ansible]: https://www.ansible.com/ "Ansible"