# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.define :lamp do |lamp|
    lamp.vm.box = "debian/buster64"
    lamp.vm.hostname = "servidor"
    lamp.vm.network :public_network,:bridge=>"wlp1s0"
  end
end
