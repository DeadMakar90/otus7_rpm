# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  config.vm.provision "shell", path: "pakage_build.sh"
  config.vm.provider "virtualbox" do |vb|
  	vb.memory = "1024"
  	vb.cpus = "1"
  end
end
