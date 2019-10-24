# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.define :lamp do |lamp|
    lamp.vm.box = "debian/buster64"
    lamp.vm.hostname = "servidor"
    lamp.vm.network :public_network,:bridge=>"wlp1s0"
    #lamp.vm.network :public_network,:bridge=>"tun0"
    lamp.vm.network :private_network, ip: "192.168.100.1", virtualbox__intnet:"redmysql"
  end


  config.vm.define :nodo2 do |nodo2|
    nodo2.vm.box = "debian/buster64"
    nodo2.vm.hostname = "nodo2"
    nodo2.vm.network :public_network,:bridge=>"wlp1s0"
    nodo2.vm.network :private_network, ip: "192.168.100.2", virtualbox__intnet:"redmysql"
  end

end
