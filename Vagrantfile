# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|

  config.vm.box = "ubuntu/bionic64"
  config.vm.define "ssh-keygen"

  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    vb.gui = true

    # Define the name of the VM
    vb.name = "ssh-keygen"

    # Customize the amount of memory on the VM:
    vb.memory = "1024"
  end

  config.vm.synced_folder "./generated-keys", "/home/vagrant/generated-keys"
  
  config.vm.hostname = "ssh-keygen"

  config.vm.provision "shell", inline: <<-SHELL
    mkdir /home/vagrant/generated-keys
  SHELL
end
