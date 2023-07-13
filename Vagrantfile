# -*- mode: ruby -*-
# vim: set ft=ruby :

MACHINES = {
  :router1 => {
    :box_name => "centos/7",
    :net => [
              {ip: '10.0.10.1', adapter: 2, netmask: "255.255.255.252", virtualbox__intnet: "r1-r2"},
              {ip: '10.0.12.1', adapter: 3, netmask: "255.255.255.252", virtualbox__intnet: "r1-r3"},
              {ip: '192.168.10.1', adapter: 4, netmask: "255.255.255.0", virtualbox__intnet: "net1"},
              {ip: '192.168.50.10', adapter: 5}, #ansible
            ]
  },
  :router2 => {
    :box_name => "centos/7",
    :net => [
              {ip: '10.0.10.2', adapter: 2, netmask: "255.255.255.252", virtualbox__intnet: "r1-r2"},
              {ip: '10.0.11.2', adapter: 3, netmask: "255.255.255.252", virtualbox__intnet: "r2-r3"},
              {ip: '192.168.20.1', adapter: 4, netmask: "255.255.255.0", virtualbox__intnet: "net2"},
              {ip: '192.168.50.11', adapter: 5}, #ansible
            ]
  },
  :router3 => {
    :box_name => "centos/7",
    :net => [
              {ip: '10.0.11.1', adapter: 2, netmask: "255.255.255.252", virtualbox__intnet: "r2-r3"},
              {ip: '10.0.12.2', adapter: 3, netmask: "255.255.255.252", virtualbox__intnet: "r1-r3"},
              {ip: '192.168.30.1', adapter: 4, netmask: "255.255.255.0", virtualbox__intnet: "net3"},
              {ip: '192.168.50.12', adapter: 5}, #ansible
            ]
  },
}

Vagrant.configure("2") do |config|
  config.vm.box_check_update = false
  config.vm.provider :virtualbox
  config.vm.provider "virtualbox" do |vb|
    vb.memory = 256
    vb.customize [
      "modifyvm", :id,
      "--cableconnected1", "on",
      "--vram", "128"
    ]
  end
  config.vm.provision "ansible" do |ansible|
    ansible.compatibility_mode = "2.0"
    ansible.playbook = "playbook.yml"
  end
  MACHINES.each do |boxname, boxconfig|
    config.vm.define boxname do |box|
      box.vm.box = boxconfig[:box_name]
      box.vm.host_name = boxname.to_s
      boxconfig[:net].each do |ipconf|
        box.vm.network "private_network", **ipconf
      end
      box.vm.provision "shell", inline: <<-SHELL
        mkdir -p ~root/.ssh
              cp ~vagrant/.ssh/auth* ~root/.ssh
      SHELL
    end
  end
end
