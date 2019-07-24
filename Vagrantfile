# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "safespring/base"

  # box name based on smie project
  # config.vm.define "postgres"

  # ssh settings
  config.ssh.username = "admin"
  config.ssh.private_key_path = "~/.ssh/id_rsa"

  # disable default shared folder
  config.vm.synced_folder ".", "/vagrant", disabled: true

  config.vm.synced_folder ".", "/etc/ansible",
    type: "rsync",
    rsync__args: ["--verbose", "--rsync-path='sudo rsync'", "--archive"]

  # read and map ENV vars with SMIE_ prefix
  env_vars = ENV.select { |k, _| /^ANSIBLE_/i.match(k) }

  # provision
  config.vm.provision :shell, keep_color: true, env: env_vars, :inline => <<SCRIPT

SCRIPT

  # provider-specific configuration
  config.vm.provider "virtualbox" do |vb|
    vb.customize ['modifyvm', :id, '--ioapic', 'on']
    vb.cpus = 4
    vb.memory = 16384
    vb.linked_clone = true
  end
  config.vm.provider "libvirt" do |libvirt|
    libvirt.cpus = 4
    libvirt.memory = 16384
    libvirt.nested = true
    libvirt.machine_virtual_size = 100
  end
end
