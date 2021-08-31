# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

Vagrant.configure("2") do |config|
  config.vm.define "puppet" do |puppet|
    puppet.vm.provider "virtualbox" do |v|
      v.cpus = 1
      v.memory = 1050
    end
    puppet.vm.network "private_network", ip: "192.168.50.10"
    # puppet.vm.network "forwarded_port", guest: 80, host: 8000
    # puppet.vm.network "forwarded_port", guest: 8080, host: 8080
    puppet.vm.hostname = "puppet.local"
    puppet.vm.box = "centos/7"
    puppet.vm.provision "shell", inline: "rpm -Uvh https://yum.puppet.com/puppet5-release-el-7.noarch.rpm"
    puppet.vm.provision "shell", inline: "sudo yum install -y puppetserver puppet vim"
    puppet.vm.provision "shell", inline: "sudo sed -i 's/Xmx2g/Xmx512m/g' /etc/sysconfig/puppetserver"
    puppet.vm.provision "shell", inline: "sudo sed -i 's/Xms2g/Xms512m/g' /etc/sysconfig/puppetserver"
    puppet.vm.provision "shell", inline: <<-SHELL
      echo "Defaults secure_path = /sbin:/bin:/usr/sbin:/usr/bin:/opt/puppetlabs/bin" > /etc/sudoers.d/99_extra
    SHELL
    puppet.vm.provision "shell", inline: "sudo systemctl enable puppetserver"
    puppet.vm.provision "shell", inline: "sudo systemctl start puppetserver"
    puppet.vm.provision "shell", inline: %q(puppet apply -e "host {'node.local': ip => '192.168.50.20'}")
  end

  config.vm.define "agent" do |node|
    # node.vm.network "forwarded_port", guest: 5601, host: 5601 
    node.vm.network "private_network", ip: "192.168.50.20"
    node.vm.hostname = "node.local"
    node.vm.box = "centos/7"
    node.vm.provision "shell", inline: "rpm -Uvh https://yum.puppet.com/puppet5-release-el-7.noarch.rpm"
    node.vm.provision "shell", inline: "sudo yum install -y puppet vim"
    node.vm.provision "shell", inline: <<-SHELL
      echo "Defaults secure_path = /sbin:/bin:/usr/sbin:/usr/bin:/opt/puppetlabs/bin" > /etc/sudoers.d/99_extra
    SHELL
    node.vm.provision "shell", inline: %q(puppet apply -e "host {'puppet.local': ip => '192.168.50.10', host_aliases => ['puppet']}")
  end
end
