# -*- mode: ruby -*-
# vi: set ft=ruby :

unless File.exist?('parameters.yml')
  # There is no parameters.yml file so create a default one.
  `cp parameters.dist parameters.yml`
end

# Load parameters
require 'yaml'
params = YAML.load_file('parameters.yml')

Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.network "private_network", ip: "192.168.33.204"

  config.vm.synced_folder "./", "/vagrant", :nfs => true
  
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
    vb.customize ["modifyvm", :id, "--cpus", 4]
    vb.name = "POD WEB GACC"
  end

  config.ssh.forward_agent = true
  config.vm.provision "ansible" do |ansible|
    ansible.extra_vars = params
    ansible.playbook  = "ansible/provision.yml"
  end
end
