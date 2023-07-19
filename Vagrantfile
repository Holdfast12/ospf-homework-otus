# -*- mode: ruby -*-
# vim: set ft=ruby :

GLOBAL_VARS = {router_id_enable: true, symmetric_routing: true}

MACHINES = {
  :router1 => {
    :box_name => "almalinux/8",
    :net => [
              {ip: '10.0.10.1', adapter: 2, netmask: "255.255.255.252", virtualbox__intnet: "r1-r2"},
              {ip: '10.0.12.1', adapter: 3, netmask: "255.255.255.252", virtualbox__intnet: "r1-r3"},
              {ip: '192.168.10.1', adapter: 4, netmask: "255.255.255.0", virtualbox__intnet: "net1"},
              {ip: '192.168.50.10', adapter: 5}, #ansible
            ],
    :vars => {router_id: '1.1.1.1'}
  },
  :router2 => {
    :box_name => "almalinux/8",
    :net => [
              {ip: '10.0.10.2', adapter: 2, netmask: "255.255.255.252", virtualbox__intnet: "r1-r2"},
              {ip: '10.0.11.2', adapter: 3, netmask: "255.255.255.252", virtualbox__intnet: "r2-r3"},
              {ip: '192.168.20.1', adapter: 4, netmask: "255.255.255.0", virtualbox__intnet: "net2"},
              {ip: '192.168.50.11', adapter: 5}, #ansible
            ],
    :vars => {router_id: '2.2.2.2'}
  },
  :router3 => {
    :box_name => "almalinux/8",
    :net => [
              {ip: '10.0.11.1', adapter: 2, netmask: "255.255.255.252", virtualbox__intnet: "r2-r3"},
              {ip: '10.0.12.2', adapter: 3, netmask: "255.255.255.252", virtualbox__intnet: "r1-r3"},
              {ip: '192.168.30.1', adapter: 4, netmask: "255.255.255.0", virtualbox__intnet: "net3"},
              {ip: '192.168.50.12', adapter: 5}, #ansible
            ],
    :vars => {router_id: '3.3.3.3'}
  },
}

Vagrant.configure("2") do |config|
  config.vm.box_check_update = false
  config.vm.provider :virtualbox
  MACHINES.each do |boxname, boxconfig|
    config.vm.define boxname do |box|
      box.vm.box = boxconfig[:box_name]
      box.vm.host_name = boxname.to_s
      boxconfig[:net].each do |ipconf|
        box.vm.network "private_network", **ipconf
      end
      box.vm.provision "ansible" do |ansible|
        ansible.compatibility_mode = "2.0"
        ansible.playbook = boxname.to_s + ".yml"
        ansible.extra_vars = GLOBAL_VARS.merge(boxconfig[:vars])
        ansible.host_key_checking = "false"
        ansible.limit = "all"
      end
    end
  end
end
