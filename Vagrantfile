# -*- mode: ruby -*-
# vi: set ft=ruby :

vm_name = "dc001"
vm_hostname = "dc001.samdom.example.com"
ip_address = "192.168.60.5"

VAGRANTFILE_API_VERSION="2"

  Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    config.ssh.insert_key = false
    config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", "512"]
  end

# Samba server (active directory domain controller) 
  config.vm.define "#{vm_name}" do |app|
    app.vm.hostname = "#{vm_hostname}"
    app.vm.box = "geerlingguy/centos7"
    app.vm.network :private_network, ip: "#{ip_address}"
  end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "main.yml"
    ansible.host_vars = {
      "#{vm_name}" => { "ansible_host" => "#{ip_address}" }
    }
  end
end

