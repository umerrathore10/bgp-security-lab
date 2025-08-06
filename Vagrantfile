# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ripencc/bgpseclab"

  # Forward port 80 from guest to host on 127.0.0.1:8080
  config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1", id: "web"

  # VirtualBox provider settings
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
  end

  # Shell provisioner to install Ansible and configure environment
  config.vm.provision "shell", reset: true, inline: <<-SHELL
    export DEBIAN_FRONTEND=noninteractive

    # Add Ansible PPA and install Ansible Core
    add-apt-repository -y ppa:ansible/ansible
    apt-get update
    apt-get -y install ansible-core python3-packaging

    # Ensure Ansible is accessible to the vagrant user
    echo 'export PATH=$PATH:/usr/bin' >> /home/vagrant/.bashrc
    chown vagrant:vagrant /home/vagrant/.bashrc

    # Install required collections for the vagrant user
    sudo -u vagrant ansible-galaxy collection install community.general ansible.posix -p /home/vagrant/.ansible/collections
  SHELL

  # Ansible Local provisioner
  config.vm.provision "ansible_local" do |ansible|
    ansible.install = false
    ansible.compatibility_mode = "2.0"  # ✅ Explicit compatibility mode
    ansible.playbook = "ansible/site.yaml"
    ansible.inventory_path = "ansible/inventory.ini"
    ansible.verbose = false
    ansible.limit = "all"
    ansible.extra_vars = {
      ansible_collections_paths: "/home/vagrant/.ansible/collections"
    }
  end
end
