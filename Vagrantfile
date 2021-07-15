# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  config.vm.define "vm_lab" do |node|
    node.vm.box = "centos/7"
    node.vm.hostname = "nginx"
    node.vm.network "private_network", ip: "10.1.1.10"
      node.vm.provider "virtualbox" do |v|
        v.cpus = 1
        v.memory = 256
    end
  end
end
