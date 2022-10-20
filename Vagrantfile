$script_docker = <<-SCRIPT
  apt-get update && \
  curl -fsSL https://get.docker.com -o get-docker.sh && \
  sudo sh get-docker.sh
SCRIPT

$script_manager = <<-SCRIPT
  docker swarm init --advertise-addr 192.168.1.100 > /configs/token.txt && \
  echo '#!/bin/bash' > /configs/token.sh && \
  echo -e "\nsudo \\" >> /configs/token.sh && \
  cat /configs/token.txt | tail -n 5 | head -n 3 >> /configs/token.sh && \
  sudo chmod +x /configs/token.sh
SCRIPT

$script_node = <<-SCRIPT
  cd /configs && \
  ./token.sh
SCRIPT


Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"

  config.vm.define "master" do |master|
    master.vm.hostname "master"
    master.vm.network "public_network", ip: "192.168.1.100"
    master.vm.synced_folder ".", "/configs"
    master.vm.synced_folder ".", "/vagrant", disabled: true
    master.vm.provision "shell", inline: $script_docker
    master.vm.provision "shell," inline: $script_manager
    master.vm.provider "virtualbox" do |vb|
        vb.name = "master"
        vb.memory = "1024"
        vb.cpus = "1"
        
  end

  (1..2).each do |i|
    config.vm.define "node0#{i}" do |node|
      node.vm.hostname "node0#{i}"
      node.vm.network "public_network", ip: "192.168.1.10#{i}"
      node.vm.synced_folder ".", "/configs"
      node.vm.synced_folder ".", "/vagrant", disabled: true
      node.vm.provision "shell", inline: $script_docker
      node.vm.provision "shell", inline: $script_node
      node.vm.provider "virtualbox" do |vb|
         vb.name = "node0#{i}"
         vb.memory = "1024"
         vb.cpus = "1"
    end
  end
end