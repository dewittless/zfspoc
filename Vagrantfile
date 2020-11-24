# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

current_dir    = File.dirname(File.expand_path(__FILE__))

Vagrant.configure(2) do |config|

  # Create the ZFS server
  config.vm.define "zfs" do |zfs|
    config.ssh.insert_key = false
    config.vm.synced_folder  ".", "/vagrant", disabled:true
    zfs.vm.box = "centos/7"
    zfs.vm.box_version = "2004.01"
    zfs.vm.hostname = "zfs"
    zfs.vm.network :private_network, ip: "192.168.60.31"
    zfs.vm.network :private_network, ip: "192.168.60.32"
    zfs.vm.provider "virtualbox" do |vb|
      vb.customize ["storagectl", :id, "--name", "SATA", "--add", "sata", "--controller", "IntelAHCI"]
      vb.memory = 1024

      (1..10).each do |i|
        datadisk = "#{current_dir}/drives/data#{i}.vdi"
        unless File.exist?(datadisk)
          vb.customize ['createhd', '--filename', datadisk, '--variant', 'Fixed', '--size', 4 * 1024]
        end
        vb.customize ['storageattach', :id,  '--storagectl', 'SATA', '--port', "#{i}", '--device', 0, '--type', 'hdd', '--medium', datadisk]
      end
    end
  end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbook.yml"
    ansible.compatibility_mode="2.0"
  end
end
