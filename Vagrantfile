# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.box = "ubuntu/trusty64"

  config.vm.provider :virtualbox do |v, o|
    o.ssh.insert_key = false
    v.customize ["modifyvm", :id, "--memory", 512]
  end

  config.vm.define "repo-cache" do |machine|
    machine.vm.network :private_network, ip: "10.64.0.2",
      :netmask => "255.255.0.0"
    machine.vm.hostname = "repo-cache"
    config.vm.provision "ansible" do |ansible|
      ansible.playbook = "playbooks/repo-cache.yml"
    end
  end

  config.vm.define "ubuntu" do |machine|
    machine.vm.network :private_network, ip: "10.64.0.3",
      :netmask => "255.255.0.0"
    machine.vm.hostname = "client1"
    machine.vm.provider :virtualbox do |v, o|
      o.vm.box = "ubuntu/trusty64"
      o.vm.provision "shell", inline: "apt-get update; apt-get --yes install avahi-daemon"
      o.vm.provision "shell", inline: "sed -i.bak 's/archive\.ubuntu\.com/repo-cache.local/g' /etc/apt/sources.list"
    end
  end

  config.vm.define "debian" do |machine|
    machine.vm.network :private_network, ip: "10.64.0.4",
      :netmask => "255.255.0.0"
    machine.vm.hostname = "debian"
    machine.vm.provider :virtualbox do |v, o|
      o.vm.box = "debian/jessie64"
      o.vm.provision "shell", inline: "apt-get update; apt-get --yes install avahi-daemon"
      o.vm.provision "shell", inline: "sed -i.bak 's/httpredir.debian.org/repo-cache.local/g' /etc/apt/sources.list"
    end
  end

  config.vm.define "centos" do |machine|
    machine.vm.network :private_network, ip: "10.64.0.5",
      :netmask => "255.255.0.0"
    machine.vm.hostname = "centos"
    machine.vm.provider :virtualbox do |v, o|
      o.vm.box = "centos/7"
      o.vm.provision "shell", inline: "rpm -Uvh --replacepkgs http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm;"
      o.vm.provision "shell", inline: "yum install avahi nss-mdns -y;"
      o.vm.provision "shell", inline: "sed -i.bak 's/mirror\.centos\.org/repo-cache.local/g' /etc/yum.repos.d/*.repo"
      o.vm.provision "shell", inline: "sed -i.bak 's/^mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-Base.repo"
      o.vm.provision "shell", inline: "sed -i.bak 's/^#Baseurl/Baseurl/g' /etc/yum.repos.d/CentOS-Base.repo"
      service avahi-daemon start)
    end
  end
end
