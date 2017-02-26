# -*- mode: ruby -*-
# vi: set ft=ruby :
require 'yaml'

######################################################
# README
######################################################
#
# If using rync to share project directory, remember to
# run `rsync-auto` to 'watch' project directory files
# and rsync automatically.
#
# Important notice - If box is destroyed and restored,
# ensure known_hosts is updated to reflect: I.e. run:
# `ssh-keygen -R YOUR_IP_OR_DOMAIN_HERE`
#

# Import Vagrant variable values from Ansible YAML files
hosts = YAML.load_file 'hosts/hosts.yml'
settings = YAML.load_file 'group_vars/dev.yml'

# Set variables
machines = hosts['dev']['hosts']
domain = settings['domain']
user = settings['user']
sync_dir = "/var/www/#{user}"

Vagrant.configure("2") do |config|

    # Install required plugins
    required_plugins = %w( vagrant-hostsupdater vagrant-vbguest vagrant-cachier )
    required_plugins.each do |plugin|
        exec "vagrant plugin install #{plugin};vagrant #{ARGV.join(" ")}" unless Vagrant.has_plugin? plugin || ARGV[0] == 'plugin'
    end

    # Vagrant cachier (cache to box scope)
    config.cache.scope = :box

    # VM instance
    machines.each do |i|
        config.vm.define "#{i.first}" do |node|

            # OS
            node.vm.box = "ubuntu/xenial64"

            # System specs
            node.vm.provider "virtualbox" do |v|
                v.memory = 4096
                v.cpus = 2
                v.customize ["modifyvm", :id, "--ioapic", "on"]

                # Use host DNS over external
                v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
                v.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
            end

            # Networking
            node.vm.network :private_network, ip: "#{i.first}"
            node.vm.hostname = "#{domain}"

            # File syncing - Note that rsync is the fastest method
            #node.vm.synced_folder ".", "#{sync_dir}", id: "vagrant-root", :owner => "www-data", :group => "www-data"
            #node.vm.synced_folder ".", "#{sync_dir}", :nfs => true, id: "vagrant-root"
            node.vm.synced_folder ".", "#{sync_dir}", type: "rsync",
                rsync__exclude: ".git/",
                id: "vagrant-root",
                :owner => "ubuntu",
                :group => "www-data",
                mount_options: ["dmode=775,fmode=775"]

        end
    end

    # Provisioning
    config.vm.provision "ansible_local" do |ansible|
        # VM definition (i.e. 'machine') is passed as host name
        # to Ansible implicitly. Make sure host name in hosts
        # file matches up with VM definition. Playbook is also
        # found on the VM when using ansible_local (due to
        # Ansible being run on the machine).
        ansible.provisioning_path = "#{sync_dir}"
        ansible.inventory_path = "#{sync_dir}/hosts"
        ansible.playbook = "#{sync_dir}/deploy.yml"
        ansible.limit = "dev" # Vagrant passes Ansible limit flag without value otherwise
        ansible.extra_vars = { # Tell ansible to operate on machine it's running from (use with ansible_local)
            ansible_connection: "local"
        }
    end

end