$script_docker = <<-SCRIPT
  apt-get update && \
  curl -fsSL https://get.docker.com -o get-docker.sh && \
  sudo sh get-docker.sh
SCRIPT

$script_manager = <<-SCRIPT
  sudo docker swarm init --advertise-addr 192.168.0.100 > /configs/token.txt && \
  echo '#!/bin/bash' > /configs/token.sh && \
  echo "sudo \\\\" >> /configs/token.sh && \
  cat /configs/token.txt | tail -n 4 | head -n 1 >> /configs/token.sh && \
  sudo chmod +x /configs/token.sh
SCRIPT

$script_node = <<-SCRIPT
  cd /configs && \
  ./token.sh
SCRIPT


Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "1024"
    vb.cpus = "1"
  end

  config.vm.define "master" do |master|
    master.vm.network "public_network", ip: "192.168.0.100", bridge: "wlp2s0"
    master.vm.hostname = "master"
    master.vm.synced_folder "./configs", "/configs"
    master.vm.synced_folder ".", "/vagrant", disabled: true
    master.vm.provision "shell", inline: $script_docker
    master.vm.provision "shell", inline: $script_manager
    
  end

  (1..2).each do |i|
    config.vm.define "node0#{i}" do |node|
      node.vm.network "public_network", ip: "192.168.0.10#{i}", bridge: "wlp2s0"
      node.vm.hostname = "node0#{i}"
      node.vm.synced_folder "./configs", "/configs"
      node.vm.synced_folder ".", "/vagrant", disabled: true
      node.vm.provision "shell", inline: $script_docker
      node.vm.provision "shell", inline: $script_node
    end
  end
end