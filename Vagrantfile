# -*- mode: ruby -*-
# vi: set ft=ruby :
vm_image = "ubuntu/jammy64"
vm_name_prefix = "kubes"
vm_subnet = "192.168.121."
# Master node configuration
master_cpus = 2
master_memory = 4096
# Worker node configurations
worker_configs = [
  {name: "argocd-node", cpus: 2, memory: 4096},
  {name: "ingress-controller", cpus: 2, memory:  4096},
  {name: "node1", cpus: 2, memory:  4096},
  {name: "node2", cpus: 2, memory: 4096},
  {name: "backup", cpus: 2, memory: 4096}
]
nginx_configs = [
  {name: "master", cpus: 2, memory: 2048},
  {name: "backup", cpus: 2, memory: 2048}
]
vm_script = <<-SCRIPT
  # Change the repository to Kakao
  sed -i 's/archive.ubuntu.com/mirror.kakao.com/g' /etc/apt/sources.list
  sed -i 's/security.ubuntu.com/mirror.kakao.com/g' /etc/apt/sources.list
  # Password Authentication for SSH
  sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
  sed -i 's/KbdInteractiveAuthentication no/KbdInteractiveAuthentication yes/g' /etc/ssh/sshd_config
  systemctl reload ssh
  # Update and upgrade packages
  sudo apt update && sudo apt upgrade -y
  sudo apt install -y python3 python3-pip git
  # Install Ansible with sudo
  sudo pip3 install --user ansible
  # Update PATH for Ansible
  echo 'export PATH=$PATH:~/.local/bin' >> ~/.bashrc
  source ~/.bashrc
SCRIPT

Vagrant.configure("2") do |config|
  # Master node configuration (private network)
  config.vm.define "#{vm_name_prefix}-master" do |master|
    master.vm.box = vm_image
    master.vm.hostname = "#{vm_name_prefix}-master"
    master.vm.network "private_network", ip: "#{vm_subnet}10", nic_type: "virtio"
    master.vm.provider "virtualbox" do |vb|
      vb.name = "#{vm_name_prefix}-master"
      vb.cpus = master_cpus
      vb.memory = master_memory
    end
    master.vm.provision "shell", inline: vm_script
  end

  # Worker nodes configuration
  worker_configs.each_with_index do |worker, index|
    config.vm.define "#{vm_name_prefix}-#{worker[:name]}" do |node|
      node.vm.box = vm_image
      node.vm.hostname = "#{vm_name_prefix}-#{worker[:name]}"
      node.vm.network "private_network", ip: "#{vm_subnet}2#{index+1}", nic_type: "virtio"
      node.vm.provider "virtualbox" do |vb|
        vb.name = "#{vm_name_prefix}-#{worker[:name]}"
        vb.cpus = worker[:cpus]
        vb.memory = worker[:memory]
      end
      node.vm.provision "shell", inline: vm_script
    end
  end

  # NGINX reverse proxy server configurations (public network)
  nginx_configs.each_with_index do |reverseproxy, index|
    config.vm.define "#{vm_name_prefix}-nginx-#{reverseproxy[:name]}" do |nginx|
      nginx.vm.box = vm_image
      nginx.vm.hostname = "#{vm_name_prefix}-nginx-#{reverseproxy[:name]}"
      # Set public network with a specified IP for nginx reverse proxy
      nginx.vm.network "public_network", bridge: "enp0s3", ip: "121.160.41.52"  # Set public IP here
      nginx.vm.provider "virtualbox" do |vb|
        vb.name = "#{vm_name_prefix}-nginx-#{reverseproxy[:name]}"
        vb.cpus = reverseproxy[:cpus]
        vb.memory = reverseproxy[:memory]
      end
      nginx.vm.provision "shell", inline: <<-SCRIPT
        # Update package list and install NGINX
        apt-get update -y
        apt-get install -y nginx
        apt install net-tools
        apt-get install -y certbot python3-certbot-nginx

        # Configure NGINX as a reverse proxy
        echo 'server {
          listen 80;

          location / {
              proxy_pass http://192.168.121.10; # Master node IP
              proxy_set_header Host $host;
              proxy_set_header X-Real-IP $remote_addr;
              proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
              proxy_set_header X-Forwarded-Proto $scheme;
          }
        }' > /etc/nginx/sites-available/default

        # Restart NGINX to apply the configuration
        systemctl restart nginx
      SCRIPT
    nginx.vm.provision "shell", inline: vm_script
    end
  end
end
