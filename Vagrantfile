# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "safespring/base"

  # ssh settings
  config.ssh.username = "admin"
  config.ssh.private_key_path = "~/.ssh/id_rsa"

  config.vm.synced_folder ".", "/vagrant", type: "rsync"

  # install git and update using ansible
  config.vm.provision :shell, :keep_color => true, :inline => <<SCRIPT
  export PYTHONUNBUFFERED=1
  export ANSIBLE_FORCE_COLOR=true
  ansible-playbook \
    -e hostname_name=dev-test-devstack-1 \
    -e role=hostname /etc/ansible/common.yaml
  ansible-playbook \
    -e packages_install=git,bridge-utils \
    -e packages_update=True \
    -e packages_clean=True \
    -e role=packages /etc/ansible/common.yaml || true
SCRIPT

  # run devstack
  config.vm.provision :shell, :keep_color => true, :privileged => false, :inline => <<SCRIPT
  sudo pip install --upgrade pip
  sudo pip install --upgrade setuptools
  git clone https://opendev.org/openstack/devstack.git
  cp /vagrant/local.conf devstack/local.conf
  cd devstack
  git checkout stable/stein
  ./stack.sh
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

#!/bin/bash

# Use a more recent pip

