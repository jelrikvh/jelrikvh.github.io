# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"

  config.vm.network :private_network, ip: "192.168.42.43"

  config.vm.synced_folder ".", "/vagrant"

  # config.vm.provision :puppet do |puppet|
  #   puppet.manifests_path = ".support/puppet/manifests"
  #   puppet.modules_path   = ".support/puppet/modules"
  #   puppet.manifest_file  = "init.pp"
  # end
end
