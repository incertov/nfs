# -*- mode: ruby -*-
# vi: set ft=ruby :

home = ENV['HOME']

Vagrant.configure(2) do |config|

  config.vm.define "srv" do |main|
    main.vm.network "private_network", adapter: 2, :device => "eth1", ip: '10.10.10.2', netmask: "255.255.255.0" # Internal network
    main.vm.hostname = "srv"
    main.vm.box = "centos/7"
    main.vm.box_version = "1905.1"
    main.vm.provider :virtualbox do |virtualbox|
      virtualbox.name = "srv"
      virtualbox.customize ["modifyvm", :id, "--cpus", "1"]
      virtualbox.customize ["modifyvm", :id, "--memory", "1024"]
      virtualbox.customize ["modifyvm", :id, "--audio", "none"]
     end
  end

  config.vm.define "ws1" do |main|
    main.vm.network "private_network", adapter: 2, :device => "eth1", ip: '10.10.10.3', netmask: "255.255.255.0" # Internal network
    main.vm.hostname = "ws1"
    main.vm.box = "centos/7"
    main.vm.box_version = "1905.1"
    main.vm.provider :virtualbox do |virtualbox|
      virtualbox.name = "ws1"
      virtualbox.customize ["modifyvm", :id, "--cpus", "1"]
      virtualbox.customize ["modifyvm", :id, "--memory", "1024"]
      virtualbox.customize ["modifyvm", :id, "--audio", "none"]
    end
  end

  

  config.vm.provision "shell", run: "always", inline: <<-SHELL
    mkdir -p ~root/.ssh
    cp ~vagrant/.ssh/auth* ~root/.ssh
  SHELL

  
end


