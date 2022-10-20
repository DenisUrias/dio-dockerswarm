$script_docker = <<-SCRIPT
  apt-get update && \
  curl -fsSL https://get.docker.com -o get-docker.sh && \
  sudo sh get-docker.sh
SCRIPT

$script_manager = <<-SCRIPT
  docker swarm init --advertise-addr 192.168.1.100 > /configs/token.txt && \
  echo '#!/bin/bash' > token.sh && \
  echo -e "\nsudo \\" >> token.sh && \
  cat token.txt | tail -n 5 | head -n 3 >> token.sh && \
  sudo chmod +x token.sh
SCRIPT



Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"

  config.vm.define "master" do |master|
    master.vm.network "public_network", ip: "192.168.1.100"
    master.vm.synced_folder "./configs", "/configs"
    master.vm.synced_folder ".", "/vagrant", disabled: true
    master.vm.provision "shell", inline: $script_docker
    master.vm.provision "shell," inline: $script_manager
        
  end
end