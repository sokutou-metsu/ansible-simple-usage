# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

# for Virtual Box
BOX_NAME = "opscode-centos-7.0"
BOX_URI = "http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_centos-7.0_chef-provisionerless.box"

VAGRANT_RAM   = 1024
VAGRANT_CORES = 1


$base_script = <<SCRIPT
set -x

vagrant_home=/home/vagrant

mkdir -p ${vagrant_home}/.ssh
cp /vagrant/ssh_key/id_rsa ${vagrant_home}/.ssh/
cp /vagrant/ssh_key/id_rsa.pub ${vagrant_home}/.ssh/

touch ${vagrant_home}/.ssh/authorized_keys
cat /home/vagrant/.ssh/id_rsa.pub >> ${vagrant_home}/.ssh/authorized_keys

chown -R vagrant:vagrant ${vagrant_home}/.ssh
chmod 600 ${vagrant_home}/.ssh/authorized_keys
chmod 600 ${vagrant_home}/.ssh/id_rsa
SCRIPT

$controller_script = <<SCRIPT
set -x

yum -y update
yum -y install epel-release
yum -y --enablerepo=epel install ansible
SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = BOX_NAME
  config.vm.box_url = BOX_URI

  config.vm.define "control-machine" do |controller|
    controller.vm.hostname = "controller"
    controller.vm.network :private_network, ip: "10.0.16.21"

    controller.vm.provision :shell, :inline => $base_script + $controller_script
  end

  config.vm.define "managed-node" do |node|
    node.vm.hostname = "node1"
    node.vm.network :private_network, ip: "10.0.16.31"

    node.vm.provision :shell, :inline => $base_script
  end

  config.vm.provider :virtualbox do |vb, override|
    vb.customize ["modifyvm", :id, "--memory", VAGRANT_RAM]
    vb.customize ["modifyvm", :id, "--cpus", VAGRANT_CORES]
  end
end
