# -*- mode: ruby -*-
# vi: set ft=ruby :
# Please install Vagrant latest version
# https://www.vagrantup.com/downloads.html
# After installing Vagrant Please install the following Plugins by executing the below commands
# vagrant plugin install vagrant-cachier vagrant-clean vagrant-disksize vagrant-vbguest

Vagrant.configure(2) do |config|
  # Check for missing plugins
  required_plugins = %w(vagrant-disksize vagrant-vbguest vagrant-clean)
  plugin_installed = false
  required_plugins.each do |plugin|
    unless Vagrant.has_plugin?(plugin)
      system "vagrant plugin install #{plugin}"
      plugin_installed = true
    end
  end

  # If new plugins installed, restart Vagrant process
  if plugin_installed === true
    exec "vagrant #{ARGV.join' '}"
  end

  config.vm.box = "ubuntu/bionic64"
  config.vm.network "private_network",  ip: "192.168.30.110"
  config.vm.hostname = "vault"
  config.disksize.size = '10GB'
  config.vm.provision "file", source: "config.json", destination: "/tmp/config.json"
  config.vm.provision "file", source: "vault.service", destination: "/tmp/vault.service"

  config.vm.provider "virtualbox" do |vb|
    vb.name = 'vault'
    vb.memory = 2048
    vb.cpus = 4
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
    vb.customize ["modifyvm", :id, "--ioapic", "on"]
	vb.gui = false

  end

  config.vm.provision "shell",privileged: true, inline: <<-SHELL
    apt-get install curl jq unzip -y
    cd /opt/
#   wget https://releases.hashicorp.com/vault/1.0.3/vault_1.0.3_linux_amd64.zip
    wget https://releases.hashicorp.com/vault/1.4.2/vault_1.4.2_linux_amd64.zip
    unzip vault_*.zip
    cp vault /usr/bin/
    mkdir /etc/vault
    mkdir /vault-data
    mkdir -p /logs/vault/
    mv /tmp/config.json /etc/vault/
    mv /tmp/vault.service /etc/systemd/system/
    echo 'export VAULT_ADDR=http://127.0.0.1:8200' >> /home/vagrant/.bashrc
    source /home/vagrant/.bashrc
    systemctl start vault.service
    systemctl enable vault.service
	vault --version
  SHELL

end
