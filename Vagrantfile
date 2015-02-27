# -*- mode: ruby -*-
# vi: set ft=ruby :

CONFIG_VERSION = 2

Vagrant.configure(CONFIG_VERSION) do |config|
  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "ubuntu/trusty64"

  config.vm.box_check_update = true


  # Forwards port 443 from the guest to the host on https://localhost:5990
  config.vm.network "forwarded_port", guest: 443, host: 5990

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.memory = "1024"
  end

  # To run the provisioner again, just run `vagrant provision`
  # To provision directly with Ansible:
  #
  # $ ansible-playbook -i .vagrant/provisioners/ansible/inventory/vagrant_ansible_inventory --private-key=~/.vagrant.d/insecure_private_key -u vagrant playbook.yml
  # See: http://docs.ansible.com/guide_vagrant.html

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "ripple-rest.yml"

    # For debugging, enable verbose output
    # ansible.verbose = "vvvv"

    # For specifying only certain roles
    # ansible.tags = ['nginx','ripple-rest']
    # ansible.tags = ['ripple-rest']
    # ansible.tags = ['newrelic']

    ansible.groups = {
      "webservers" => ["default"]
    }

    ansible.extra_vars = {
      # Override the Ansible configured SSH user
      ansible_ssh_user: 'vagrant',
      remote_user: 'vagrant',
      version: 'develop'
    }
  end
end
